통합 테스트는 독립적으로 개발된 소프트웨어의 단위가 서로 연결되어 있을 때 올바르게 작동하는지 확인합니다. 이 용어는 소프트웨어 산업의 확산된 표준임에도 불구하고 이 용어를 글에 사용하는 것이 조심스러웠습니다. 특히 많은 사람들이 반드시 통합 테스트의 범위는 넓어야 한다고 하지만, 좁은 범위에서 더 효과적으로 수행할 수 있습니다

흔히 이러한 것은 약간의 역사로 시작하는 것이 가장 좋습니다. 통합 테스트를 맨처음 알았을 때는 1980년대 폭포수(Waterfall) 개발 방법론의 지배적인 영향때문이였습니다. 큰 프로젝트에서는 시스템의 다양한 모듈의 인터페이스와 동작을 명시하는 설계 단계가 있습니다. 모듈들을 개발자들이 할당받아 프로그래밍합니다. 보통 한명의 개발자가 하나의 모듈만 담당하는 일은 흔치 않는 일이지만, 그것을 개발(build)를 하는데 몇달이 걸릴 만큼 충분히 큰 모듈일 수도 있습니다. 이러한 모든 작업은 독립적으로 진행했고, 프로그래머가 개발이 모두 끝났다고 생각했을때 QA에게 테스트를 위해 모듈을 전달했습니다.

첫번째 부분은 설계 단계에서 요구사항이 동작되는지 확인하는 자체 모듈을 테스트 하는 단위(Unit) 테스트 입니다. 완료되면, 다양한 모듈들이 전체 시스템 또는 중요한 하위 시스템에 결합하여 통합테스트를 진행합니다.

이름에서 알 수 있듯이 통합테스트의 포인트는 **많은 분리되어 개발된 모듈들이 함께 예상한 동작을 하는지** 입니다. 여러 모듈을 활성화하고 모두에 대해 더 높은 수준의 테스트를 실행하여 함께 작동하는지 보장하는 방식으로 수행되었습니다. 이러한 모듈은 실행 파일의 일부이거나 분리된 것일 수가 있습니다.

2010년대의 관점에서 살펴보면 다음 두 가지가 특징이 합쳐지게 되었습니다.

- 별도로 개발된 모듈이 제대로 작동하는 테스트
- 여러 모듈이 예상대로 작동하는지 테스트

위의 두가지를 쉽게 통합할 수 있었지만, 결국 frobil과 twibbler 모듈을 모두 단일 환경에서 활성하지 않고 두 모듈을 모두 실행하는 테스트를 수행할 수 있을까요?

2010년대 관점은 1980년대에 전혀 고려하지 않은 다른 대안을 제공합니다. 그 대안은 twibbler의 **[테스트 더블](https://martinfowler.com/bliki/TestDouble.html)** 대신에 twibbler와 함께 상호작용하는 frobile안에서 일부분을 수행함으로써 frobile와 twibbler의 모듈들을 통합 테스트를 진행합니다. Test Double을 제공하면 믿을수 있는 twibber의 Test Double이 되며, 우리는 모든 twibbler 인스턴스 실행하지 않고 twibbler의 상호작용을 모두 테스트할 수 있습니다. 모닐리틱 어플리케이션의 별도 모듈인 경우에는 큰 문제가 되지 않지만, twibbler가 네트워크, 환경, 빌드 툴을 요구하는 별도의 서비스인 경우에는 큰 문제입니다. 서비스의 경우에는 in-process test double 또는 over-the-wire double을 [mountebanck](http://www.mbtest.org/)와 같은 것을 사용해서 실행할 수 있습니다.

통합 테스트의 명백한 문제는 TestDouble이 진짜로 괜찮은 Test Dobulde인지 입니다.

그러나 우리는 별도로 [ContractTests](https://martinfowler.com/bliki/ContractTest.html)를 사용할 수 있습니다.

Teams that do this, may still do some form of end-to-end system test with all real services, but if so it's only a final smoke test with a very limited range of paths tested. It also helps to have a mature QA in Production capability, and if that is mature enough, there may be no end-to-end system testing done at all.

narrow Integration Test와 Contract Test를 조합해서 사용하면, 서비스의 실제 인스턴스에 대한 테스트 없이 외부 서비스에 대한 통합 테스트에 대해 의심하지 않을수 있어 빌드 프로세스를 크게 쉬워진다. 이 작업을 수행하는 팀은 모든 실제 서비스에 대해 end-to-end System Test의 어떠한 형태로도 수행할 수 있지만, 제한된 범위의 경로를 테스트를 한 smock test일 뿐입니다. 또한 성숙한 프로덕션의 QA를 할 수 있게 도와주지만, 모든 end-to-end System Test가 전혀 수행되지 않을 수 있습니다.

![https://martinfowler.com/bliki/images/integrationTesting/sketch.png](https://martinfowler.com/bliki/images/integrationTesting/sketch.png)

- 통합 테스트는 일반적으로 많은 모듈이 실행된 상태에서 실행하는 광범위한 테스트를 말합니다.
- 그러나 그것은 개별적인 Test dobule와 함께 상호작용의 좁은 테스트를 하는 것으로 실행될 수 있다.
- Test Double의 진실성을 보장받기 위해 Contract Tests에 의해 지원받게 된다.

문제는 통합 테스트를 구성하는 것은 (적어도) 두가지 다른 개념이 있다는 것입니다.

좁은(narrow) 의미의 통합 테스트

- 별도의 서비스와 통신하는 내 서비스의 코드 일부분만 실행한다.
- 프로세스 또는 원격 서비스 둘다 test dobule을 사용한다.
- 따라서 단위 테스트 이하의 범위로 많은 좁은 범위의 테스트로 구성한다.(일반적으로 단위 테스트에 사용되는 동일한 테스트 프레임워크로 실행합니다.)

광범위한(board) 통합 테스트

- 모든 서비스들의 라이브 버전이 필요하며, 실제 테스트 환경과 네트워크 접근이 필요하다.
- 수행하는 코드가 상호작용을 담당하는것 뿐만 아니라 모든 서비스를 통해 코드 경로를 수행합니다.

그리고 많은 개발자들은 "통합 테스트"가 "광범위한 통합 테스트"만 의미한다고 하며, 좁은 통합테스트를 사용하는 사람들과 많은 혼란이 발생합니다.

만약 너의 유일한 통합 테스트가 광범위한 테스트인 경우라면 유연성, 사용 편의성, 테스트 성능을 상당히 향상시키기 위해서는 좁은 통합 테스트를 해보는걸 고려해보는게 좋다. 좁은 통합 테스트는 범위가 제한되어 있기 때문에 그들은 매우 빠르게 실행되고 [DeploymentPipeline](https://martinfowler.com/bliki/DeploymentPipeline.html)를 초기 단계에서 실행시킬수 있어 빨간색을 보면서 빠르게 피드백을 받을 수 있다.

모두 이것이 "통합 테스트"대해 주의해야하는 이유입니다. 글을 읽을때, 좀더 맥락을 찾아서 작가가 말하려는 실제 의미를 알아갑니다. 나는 광범위한 통합테스트를 말할때, system test 또는 end-to-end test를 선호합니다. 나는 좁은 통합 테스트에 대한 좀더 좋은 이름가 없기 때문에 나는 그냥 사용하고 있습니다. (그러나 독자에게 이러한 특성의 테스트의 이해를 돕기 위해 narrow를 사용합니다.) 

## **Notes1:** Although I prefer to focus

빌드 된 모듈의 개별 상호작용에 대한 정의에 초점을 맞추고 싶지만, 때때로 단위 테스트보다 큰 의미로 사용되는 통합테스트를 보게 됩니다. 그리고 고립적인 단위 테스트의 일부 사용자들은 통합 단위 테스트를 통합 테스트에 묘사합니다.

## 원본 글

- [IntegrationTest](https://martinfowler.com/bliki/IntegrationTest.html)
