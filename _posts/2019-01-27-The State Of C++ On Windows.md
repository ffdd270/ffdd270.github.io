---
layout: post
title: The State Of C++ on Windows
---

# The State Of C++ On Windows

[원문](https://kennykerr.ca/2019/01/25/the-state-of-cpp-on-windows/?fbclid=IwAR3DGj2bFhGLm83gDPizrYDtRCRwP-IoAsl6ABtR9HLISw2yJE1D9eTBXwo)

2019년 국정 연설(미국 국정 연설. - 역주)이 연기되었으니. 제가 그 틈을 타 Windows의 C++ 소식을 알려드리겠습니다! ( 미연방 국정 연설은 영어로 State Of Union Address인데, 원작자가 말장난으로 State Of C++을 썼습니다. 한국어로 번역하면 윈도우 C++ 국정 연설정도 됩니다. - 역주)

C++은 여전히 중요한 프로그래밍 언어고, Windows 플렛폼에서 결과를 얻을 수 있는 유일한 시스템 프로그래밍 언어입니다. Visiual Studio 2019 릴리즈에서도 몇 주 동안 공유할 수 있는 놀라운 정보들이 있지만. 오늘은 C++ / WinRT에 대한 업데이트를 알려드릴려고 합니다.

C++/WinRT는 개발자들이 현대적이고 표준적인 C++을 지향하면서 WRL([윈도우 런타임 라이브러리](https://en.wikipedia.org/wiki/Windows_Runtime#C.2B.2B_.28WRL.2C_Component_Extensions.2C_C.2B.2B.2FWinRT.29))와 C++ / CX를 피하게 되며 더 많이 쓰이고 있습니다. 최근엔 Windows UI 라이브러리가 오픈 소스라는 소식을 들으셨을 건데, 그게 C++ / WinRT로 만들었다는 걸 알아차리셨습니까? 이건 그저 한 가지 중요한 예시일 뿐이지만, C++/WinRT로 전환한 많은 팀들이 있고. 우리는 앞으로 WRL이나 C++/CX를 사용하지 못하게 할 것입니다.

C++ / WinRT가 XAML의 많은 부분을 구현하는데 사용된다 하더라도, XAML 응용 프로그램을 만들 때 C++/WinRT를 채택하는 팀들이 적습니다. 이 현상은, C++ / WinRT가 가장 자주 등장하는 세 가지 범주, 혹은 세 가지 시나리오를 이해하는 데 도움이 됩니다.

1. Windows API 사용 또는 호출 (WinRT 유형 사용이라고도 함)
2. Windows API 작성 (WinRT 유형 생성이라고도 함)
3. Xaml 응용 프로그램과 컨트롤 제작

첫 번째는 C++ / WinRT를 사용하여 API를 호출 하여 블루투스, 스트리밍 비디오 통신, Window 셸과의 통합 등과 같은 작업을 처리하는 것입니다. 두 번째는 앞서 말한 기능과, 그래픽 API, 저장소 및 파일 시스템 API, 네트워킹 API등을 제공하는 API 작성을 처리하는 것이고. 세번째는 XMAL 프레임워크에 구축된 앱 및 컨트롤 작성을 처리하는 것입니다. XMAL은 UI를 작성하는 API중 하나일 뿐이지만, 현재 Window에서 지배적인 UI 프레임워크이며 WinRT보다 큰 영향력을 가지고 있기 때문에 동급 취급 받을 자격이 있습니다.

C++/WinRT는 첫번째 범주는 완벽하고, 타협하지 않고 지원합니다. 두번째 범주인 새로운 API 작성은 개발자가 API를 구현하기 전에 IDL([인터페이스 정의 언어](https://en.wikipedia.org/wiki/IDL_(programming_language)))를 사용하여 API의 구조를 정의해야 하기 때문에 조금 더 복잡합니다. 그렇지만 C++/CX를 사용할 때처럼 훨씬 복잡하지는 않으며, C++/WinRT를 사용할 때 얻는 많은 장점을 감안할때 일반적으로 매우 만족할만한 거래입니다.

다만 세 번째 범주인 응용 프로그램 제작은 얘기가 다릅니다. XMAL은 .Net용으로 설계 되었으며 그 의도대로 기능이 반영되어야 합니다. 그렇기에 C++/WinRT로 XMAL 응용 프로그램을 작성할 수는 있지만, 꽤 귀찮습니다. 이건 C++ 개선안에서 아주 중요한 곳입니다.

그렇지만 전 우리가 주변을 돌아 볼 수 있길 바랍니다. Windows가 C++ 개발자들에게 좋은 UI 개발 환경을 가지고 있지 않다는 건 저에게 아쉬움을 남깁니다. C++ 개선안은 장기적으로 이 문제를 해결하는 데 도움이 될 수 있지만, 완성될 때까지 기다릴 수 없으므로 가까운 시일안에 유용하게 사용할 수 있는 몇 가지 옵션을 생각하고 있습니다. 그 중 일부는 Visual Studio에서 더 나은 C++/WinRT 개발 경험을 경험할 수 있도록 DevDiv와의 논의는 물론, XAML의 큰 개선이 필요하지 않은 대안 접근법이 포합됩니다.

C++/WinRT와 xlang에 대한 자세한 내용을 확인하세요. 많은 일들이 벌어지고 있습니다 - 저는 이 글을 막 끝냈지만. 우리가 해온 일들을 공유하기 위해 다시 글을 작성할 것입니다.
