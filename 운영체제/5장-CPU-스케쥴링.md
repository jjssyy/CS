## CPU Scheduling

### CPU Scheduling

- 현재 Ready Queue에 있는 있는 프로세스 중 어떤 프로세스에 CPU를 줄 것인지를 결정하는 메커니즘
- 비선점형 (강제로 뺏기 불가능), 선점형 (강제로 뺏기 가능)으로 나눌 수 있음
- 현대에서는 대부분 선점형 스케줄링 사용

### Scheduling Criteria (스케쥴링 척도)

- 시스템 입장에서의 성능 척도 : cpu 하나로 최대한 일 많이 시킬수록 좋음
  - CPU utilization(이용률) : 전체 시간에서 cpu가 일한 시간, 클수록 좋음
  - Throughput(처리량) : 주어진 시간동안 처리한 일의 양, 클수록 좋음
- 프로그램 입장에서의 성능 척도 : 걸리는 시간이 짧을수록 좋음
  - Turnaround time(소요시간) : CPU를 쓰러 들어와서 다 쓰고 나갈 때까지 걸린 시간 (CPU burst가 끝날 때까지 걸린 시간)
  - Waiting time(대기 시간) : CPU를 얻기위해 기다린 시간들의 합(cpu burst기간에 ready 상태였던 모든 시간의 합)
  - Response time(응답 시간)  : 처음으로 CPU를 얻기까지의 시간 (첫 응답이 나올 때 까지 걸리는 시간)

---

### Scheduling Algorithms

#### FCFS (First-Come First-Served)

- 먼저 온 순서대로 처리
- 비선점형 스케줄링
- Convoy effect :  긴 프로세스가 먼저 와서 뒤에 있는 짧은 프로세스들이 지나치게 오래 기다려야되는 현상

#### SJF (Shortest-Job-First) 

- cpu burst time이 가장 짧은 프로세스에게 cpu 할당
- 비선점형 스케줄링, 현재 수행중인 프로세스보다 더 짧은 프로세스가 들어와도 cpu를 빼앗기지 않음

#### SRTF (Shortest-Remaining-Time-First) 

- cpu burst time이 가장 짧은 프로세스에게 cpu 할당
- 선점형 스케줄링, 현재 수행중인 프로세스보다 더 짧은 프로세스가 들어오면 그 프로세스에게 cpu 할당
- 주어진 프로세스들에 대해 minimum average wating time을 보장
- 문제점
  - starvation (기아 현상) : cpu 사용시간이 긴 프로세스는 영원히 cpu를 얻지 못할 수 있음
  - cpu 사용시간을 미리 알 수 없음 
    → 추정은 가능 (exponential averaging 사용)
    
    ![image-20210905165649154](/uploads/3f3923bd4c86bd9d68f1f18afc047220/image-20210905165649154.png)

    n+1번째 cpu 사용 예측 시간 = α * n번째 실제 cpu 사용 시간 + (1-α) * n번째 cpu 사용 예측 시간 (0 ≤ α ≤ 1)

    ![image-20210905170136568](/uploads/f720829c813f84e9f300dbe0591507f3/image-20210905170136568.png)

    현재를 기준으로 최근 과거는 가중치를 높게 반영, 오래전의 것은 낮게 반영하게 됨

#### Priority Scheduling 

- 우선순위를 가진 프로세스에게 cpu 할당
- 각  프로세스마다 우선순위를 나타내는 숫자(정수)가 주어짐 (일반적으로 작을수록 우선순위 높음)
- SJF도 일종의 우선순위 스케쥴링
- preemptive, non-preemptive
- 문제점 - Starvation (기아 현상) : 우선순위가 낮은 프로세스는 영원히 cpu를 얻지 못할 수 있음
- 해결법 - Aging (노화) :  시간이 지날수록 우선순위를 높여줌

#### RR (Round Robin)

- 현대적인 컴퓨터 시스템에서 사용하는 cpu 스케줄링은 RR에 기반함
- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
- 할당 시간이 끝나면 프로세스는 선점(preempted) 당하고 ready queue의 제일 뒤에 가서 다시 줄을 섬
- 응답시간(cpu를 최초로 얻는 시간)이 빨라짐
- cpu 사용 시간을 예측할 필요 없이 cpu를 짧게 쓰는 프로세스가 빨리 cpu를 쓰고 나갈 수 있게 해줌
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit이라고 하면 각 프로세스는 최대 q time unit 단위로 cpu 시간의 1/n을 얻음
  => 내가 적어도 n * q 만큼 기다리면 한번은 cpu를 얻을 수 있음
  => 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음
- 대기시간이 본인이 cpu를 사용하려는 시간에 비례함
- Performance
  - 할당 시간이 클 경우 => FCFS
  - 할당 시간이 작을 경우 => context switch 자주 발생, 오버헤드가 커짐
- 일반적으로 SJF보다 average turnaround time이나 wating time은 길어질 수 있지만 response time은 더 짧음

#### Multilevel Queue

- 우선 순위별로 Ready queue를 여러 개로 분할
  - ex) foreground (interactive)
  - ex) background (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - ex) foreground - RR
  - ex) background - FCFS
- 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling : 우선순위가 높은 큐부터 cpu 할당, 기아 현상 발생 가능
  - Time slice : 각 큐에 cpu time을 적절한 비율로 할당

#### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동 가능
- 에이징(aging)을 이와 같은 방식으로 구현할 수 있음
- Multilevel-feedback0queue scheduler를 정의하는 파라미터들
  - queue의 수
  - 각 큐의 scheduling algorithm
  - process를 상위 큐로 보내는 기준
  - process를 하위 큐로 내쫓는 기준
  - 프로세스가 cpu 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
- 예시

![image-20210905190335797](/uploads/bf226211a36e03d1f07741ee7680584a/image-20210905190335797.png)

- Scheduling
  1. new job이 queue Q0(quantum = 8)로 들어감
  2. cpu를 잡아서 할당시간 8ms 동안 수행
  3. 8ms 동안 다 끝내지 못했으면 queue Q1(quantum=16)으로 내려감
  4. Q1에 줄서서 기다렸다가 cpu를 잡아서 16ms 동안 수행됨
  5. 16ms에 끝내지 못한 경우 queue Q2(FCFS)로 쫓겨남

#### Multiple-Processor Scheduling

- CPU가 여러 개인 경우
- Homogeneous processor인 경우
  - queue에 한 줄로 세워서 순서대로 cpu 할당
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 문제가 복잡해짐
- Load sharing
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
  - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing (SMP)
  - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

#### Real-Time scheduling

- real-time job : deadline이 있는 job -> 데드라인 안에 수행되어야 함
- Hard real-time systems : 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
- Soft real-time computing : 일반 프로세스에 비해 높은 우선순위를 갖도록 해야 함

#### Thread Scheduling

- Local scheduling (OS가 thread의 존재 모름)
  - user level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄 할 지 결정 (프로세스 내부에서 결정)
- Global Scheduling (OS가 thread의 존재 앎)
  - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄 할 지 결정

#### Algorithm Evalutaion

- Queueing models
  - 이론적인 방법
  - 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산
- Implementation (구현) & Measurement (성능 측정)
  - 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해 성능을 측정하고 비교
- Simulation (모의 실험)
  - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
