---
createdDate: 2025-06-30
---
네, "stalled" 상태에는 종종 "initial connection"을 포함하여 여러 가지 이유로 인해 요청이 지연되는 경우가 포함됩니다.

크롬 개발자 도구의 네트워크 탭에서 "Stalled"는 요청이 실제로 전송되기 전에 대기하는 시간을 나타냅니다. 이 대기 시간은 다양한 원인으로 발생할 수 있으며, 그 중 일부는 다음과 같습니다:

- **브라우저의 동시 연결 제한 (Connection limits):** 특히 HTTP/1.1에서는 브라우저가 동일한 출처(origin)에 대해 동시에 열 수 있는 TCP 연결 수를 제한합니다 (보통 6개). 이미 최대 연결 수에 도달했다면, 새로운 요청은 연결 슬롯이 사용 가능해질 때까지 "stalled" 상태로 대기합니다. 이 경우, 초기 연결을 시도하기 전에 대기하는 것이므로 "initial connection"과 관련이 있다고 볼 수 있습니다.
    
- **프록시 협상 (Proxy negotiation):** 프록시를 사용하는 경우, 프록시와의 연결 및 협상에 시간이 소요될 수 있으며, 이 시간도 "stalled"에 포함될 수 있습니다.
    
- **큐잉 (Queueing):** "stalled"는 "queueing" 상태에서 파생될 수 있습니다. 큐잉은 요청이 더 높은 우선순위의 리소스에 의해 연기되거나, 사용 가능한 TCP 소켓을 기다리거나, 디스크 캐시 공간 할당을 기다리는 등의 이유로 대기열에 있는 상태를 의미합니다. 이 큐잉 상태 이후에 "stalled"가 될 수 있습니다.
    
- **DNS 조회, TCP 연결, SSL 핸드셰이크 등 초기 연결 설정 문제:** 이 단계에서 지연이 발생하면 요청이 실제로 서버로 보내지기 전에 멈춰있게 되므로 "stalled"로 나타날 수 있습니다. 예를 들어, DNS 조회가 오래 걸리거나, TCP 연결 설정에 문제가 있거나, SSL 핸드셰이크가 지연되는 경우 모두 "stalled" 시간에 포함될 수 있습니다.
    
- **서버 측 문제:** 서버가 요청을 처리할 준비가 안 되었거나, 서버의 스레드/워커 부족으로 요청을 즉시 처리하지 못하고 대기시키는 경우에도 클라이언트 측에서는 "stalled"로 나타날 수 있습니다.
    

요약하자면, "stalled"는 단순히 네트워크 전송이 시작되기 전의 모든 대기 시간을 포괄하는 개념이며, 여기에는 **초기 TCP 연결 설정 시도 및 이와 관련된 대기 시간**이 포함될 수 있습니다.


---
"stalled" 상태는 HTTP/2.0 이상에서도 발생할 수 있습니다. HTTP/2.0은 여러 요청을 하나의 TCP 연결 내에서 다중화하여 "Head-of-Line Blocking" 문제를 완화하고 동시 연결 제한으로 인한 지연을 줄이는 데 도움이 되지만, "stalled"가 완전히 사라지는 것은 아닙니다.

주요 내용은 다음과 같습니다:

- **HTTP/2.0에서의 Stalled 원인:**
    
    - **브라우저의 연결 제한:** HTTP/2.0은 단일 연결을 재사용하지만, 브라우저는 여전히 특정 도메인(FQDN)에 대한 동시 연결 수(일반적으로 6개)를 제한할 수 있습니다. 이 제한을 초과하는 요청은 연결 슬롯이 사용 가능해질 때까지 "stalled" 상태로 대기할 수 있습니다.
        
    - **CORS Preflight 요청 병합:** Chrome과 같은 브라우저에서는 CORS(Cross-Origin Resource Sharing) preflight 요청이 실제 요청과 병합되어 "stalled"로 보고될 수 있습니다. preflight 요청에 문제가 발생하면 실제 요청이 지연될 수 있습니다.
        
    - **서버 측 문제 또는 GOAWAY 프레임:** 서버가 요청을 처리할 준비가 되지 않았거나, 서버에서 `GOAWAY` 프레임을 보내 연결을 종료하려 할 때 클라이언트에서 "stalled"로 나타날 수 있습니다. 이는 클라이언트가 새 연결을 설정하고 데이터를 재전송해야 할 수 있음을 의미합니다.
        
    - **플로우 제어 (Flow Control):** HTTP/2.0은 스트림과 연결 전체에 대한 플로우 제어를 가지고 있습니다. 서버나 클라이언트의 윈도우 크기 제한으로 인해 데이터 전송이 일시 중지되어 요청이 "stalled"처럼 보일 수 있습니다.
        
    - **캐싱 및 재요청:** 동일한 URL을 동시에 여러 번 요청할 때, 첫 번째 요청이 캐시를 사용하도록 설정되어 있으면 두 번째 요청은 첫 번째 요청이 완료될 때까지 "stalled" 상태로 대기할 수 있습니다 (Chrome에서 발생).
        
- **HTTP/3 (QUIC)에서의 Stalled:**
    
    - HTTP/3는 TCP 대신 UDP 기반의 QUIC 프로토콜을 사용하며, 이는 TCP의 "Head-of-Line Blocking" 문제를 완전히 해결하고 연결 설정 시간을 줄여줍니다. 또한, 네트워크 경로 변경 시에도 연결을 유지하는 등 더 나은 복원력을 제공합니다.
        
    - 그럼에도 불구하고 HTTP/3에서도 "stalled" 현상이 보고될 수 있습니다. 예를 들어, VPN 사용 등으로 네트워크 경로가 변경되거나, MTU(Maximum Transmission Unit) 문제가 발생하거나, 서버 측의 QUIC 구현 문제 등으로 인해 요청이 무기한 "stalled" 상태에 빠지는 경우가 있었습니다. 이는 주로 HTTP/3의 초기 구현 문제나 특정 네트워크 환경에서의 호환성 문제와 관련이 있습니다.
        

결론적으로, HTTP/2.0 이상에서도 "stalled"는 발생할 수 있지만, 그 원인과 맥락은 HTTP/1.1과는 다를 수 있습니다. 특히 HTTP/2.0은 다중화로 인해 HTTP/1.1에서 흔히 발생하는 동시 연결 제한으로 인한 "stalled"는 줄어들지만, 다른 종류의 지연은 여전히 존재합니다. HTTP/3는 더욱 개선되었지만, 특정 상황에서는 여전히 지연이 발생할 수 있습니다.

더 자세한 정보는 다음 링크를 참조하실 수 있습니다:

- [Chrome HTTP Request stuck in "Stalled" State](https://xangelo.ca/posts/chrome-http-request-stuck-in-a-stalled-state/)
    
- [HTTP2 connections stuck when paused and resumed multiple times #10988 - GitHub](https://github.com/curl/curl/issues/10988)
    
- [Chrome stalled requests for some HTTP2 resources - Server Fault](https://serverfault.com/questions/852021/chrome-stalled-requests-for-some-http2-resources)
    
- [HTTP/3 stalls when switching to network with MTU<=1350 - Bugzilla@Mozilla](https://bugzilla.mozilla.org/show_bug.cgi?id=1734110)
    
- [Hypertext Transfer Protocol Version 3 (HTTP/3) - IETF](https://www.ietf.org/archive/id/draft-ietf-quic-http-34.html)
    
- [Requests stalling with http3 - Cloudflare Community](https://community.cloudflare.com/t/requests-stalling-with-http3/742568)