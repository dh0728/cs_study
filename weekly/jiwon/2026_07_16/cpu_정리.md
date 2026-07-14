# OS-CPU스케줄링

# CPU 스케줄링 (CPU Scheduling)

> <b>💡 한 줄 요약</b>
> CPU는 한 번에 <b>하나의 프로세스</b>만 실행할 수 있어, I/O를 기다리며 노는 시간을 다른 프로세스에게 배분하는 것이 CPU 스케줄링의 목적입니다. 어떤 프로세스를 언제 얼마나 실행시킬지 정하는 다양한 알고리즘(FCFS, SJF, Priority, RR, MLQ, MLFQ)이 있습니다.

---

## 1. 기본 개념 (Basic Concepts)

### 1.1 CPU–I/O Burst Cycle

프로세스의 실행은 두 가지 작업의 반복(cycle)입니다.

| 구간 | 설명 |
| --- | --- |
| **CPU burst** | CPU가 실제로 계산하는 구간 |
| **I/O burst** | 입출력을 기다리는 구간 |

핵심: **I/O를 기다리는 동안 CPU는 놀고 있습니다.** 이 노는 시간에 다른 프로세스에게 CPU를 주면 이용률이 올라갑니다. 이것이 multiprogramming이고, CPU 스케줄링을 하는 근본적인 이유입니다.

> 💡 통계적으로 CPU burst는 **짧은 것이 압도적으로 많고, 긴 것은 드뭅니다**. 이 사실은 뒤에 나오는 SJF, RR 같은 알고리즘 설계에 영향을 줍니다.

### 1.2 CPU Scheduler와 스케줄링이 일어나는 4가지 시점

**CPU Scheduler (Short-term Scheduler)**: 메모리에 올라와 있는 ready 상태의 프로세스들 중 하나를 골라 CPU를 할당하는 역할.

프로세스가 상태를 바꿀 때 스케줄링 결정이 일어납니다 — 시험 단골!

| 번호 | 상태 전이 | 분류 |
| --- | --- | --- |
| 1 | running → **waiting** (I/O 요청 등) | 비선점 대상 |
| 2 | running → **ready** (CPU를 뺏김) | 선점 대상 |
| 3 | waiting → **ready** (I/O 완료) | 선점 대상 |
| 4 | **terminates** (프로세스 종료) | 비선점 대상 |

- **1·4번만 일어나는 경우 = Nonpreemptive (비선점)**: 프로세스가 스스로 CPU를 내놓을 때만 교체. 한 번 잡으면 뺏기지 않음.
- **2·3번도 포함하면 = Preemptive (선점)**: OS가 강제로 CPU를 뺏을 수 있음. ready queue에 변동이 생기면 재검토.

> 💡 암기 팁: 1·4번은 프로세스가 "자발적으로" 나가는 경우 → 비선점. 2·3번은 "강제로 끌려나오거나, 새 경쟁자가 등장"하는 경우 → 선점.

### 1.3 Dispatcher (디스패처)

Short-term scheduler가 프로세스를 **골랐다면**, 실제로 CPU 제어권을 **넘겨주는 일**은 dispatcher가 합니다.

Dispatcher가 하는 일 3가지:

1. **Switching context** — 현재 프로세스의 상태를 저장하고(PCB에), 다음 프로세스 상태를 복원 (커널 모드에서 수행)
2. **Switching to user mode** — 사용자 프로그램 실행을 위해 모드 전환
3. 사용자 프로그램의 **적절한 위치로 점프**해서 재시작

> **용어 — Dispatch latency:** 한 프로세스를 멈추고 다른 프로세스를 실행시키는 데 걸리는 시간. 순수한 오버헤드(낭비)이므로 짧을수록 좋습니다.

---

## 2. 스케줄링 평가 기준 (Scheduling Criteria)

"어떤 스케줄링이 좋은 스케줄링인가?"를 판단하는 5가지 지표입니다.

| 기준 | 의미 | 목표 |
| --- | --- | --- |
| **CPU Utilization** (이용률) | CPU가 놀지 않고 일한 비율 | **Max** |
| **Throughput** (처리량) | 단위 시간당 완료된 프로세스 수 | **Max** |
| **Turnaround Time** (반환 시간) | 프로세스 도착 → 완전히 끝날 때까지 걸린 총 시간 | **Min** |
| **Waiting Time** (대기 시간) | **ready queue에서 기다린** 시간의 합 | **Min** |
| **Response Time** (응답 시간) | 요청 제출 → **첫 반응**이 나올 때까지의 시간 (완료가 아님!) | **Min** |

> ⚠️ 주의: Response time은 "출력 완료"가 아니라 "**첫 반응**"까지의 시간입니다. 시분할(time-sharing) 환경에서 중요한 지표입니다.
> Waiting time은 "ready queue에서만" 잰다는 점도 중요합니다 (I/O 기다리는 시간은 포함 안 함).

---

## 3. 스케줄링 알고리즘 (Scheduling Algorithms)

### 3-1. FCFS (First-Come, First-Served)

**먼저 온 순서대로** 처리. 구현이 가장 단순하며 **비선점**.

#### 예시

P1(burst 24), P2(3), P3(3)이 P1, P2, P3 순으로 도착:

```
| P1                     | P2 | P3 |
0                        24   27   30
```
Waiting time: P1=0, P2=24, P3=27 → 평균 = (0+24+27)/3 = **17**

같은 프로세스들이 P2, P3, P1 순으로 도착하면:

```
| P2 | P3 | P1                     |
0    3    6                        30
```
평균 = (6+0+3)/3 = **3** ← 훨씬 좋음!

> ⚠️ **핵심 문제 — Convoy Effect (호위 효과):** 긴 프로세스 뒤에 짧은 프로세스들이 줄줄이 갇혀서 기다리는 현상. 도착 순서 하나로 평균 대기시간이 17 → 3으로 바뀐 것이 그 증거.

---

### 3-2. SJF (Shortest-Job-First)

**다음 CPU burst가 가장 짧은** 프로세스부터 실행.

| 방식 | 설명 |
| --- | --- |
| **Nonpreemptive SJF** | 일단 CPU를 잡으면 그 burst가 끝날 때까지 안 뺏김. 현재 실행 중인 프로세스를 방해하지 않음 |
| **Preemptive SJF = SRTF** (Shortest-Remaining-Time-First) | 새 프로세스의 burst가 현재 실행 중인 프로세스의 **남은 시간**보다 짧으면 뺏음 |

> 💡 **SJF는 optimal** — 주어진 프로세스 집합에 대해 **평균 대기 시간이 수학적으로 최소**입니다. (짧은 일을 먼저 끝내면 뒤에서 기다리는 총량이 줄어들기 때문)

#### 예시

Non-preemptive SJF: P1(도착 0, burst 7), P2(2, 4), P3(4, 1), P4(5, 4)

```
| P1           | P3 | P2      | P4      |
0              7    8         12        16
```
시각 0에는 P1밖에 없으니 P1 실행. P1이 끝나는 시각 7에 남아있는 P2(4), P3(1), P4(4) 중 가장 짧은 P3 선택.
평균 대기 = (0 + 6 + 3 + 7)/4 = **4**

Preemptive SJF(SRTF): 같은 프로세스

```
| P1 | P2  | P3 | P2  | P4       | P1            |
0    2     4    5     7          11              16
```
시각 2: P2(4) 도착. P1의 남은 시간 5 > 4 → **P1 선점당함**
시각 4: P3(1) 도착. P2 남은 시간 2 > 1 → P2 선점당함
평균 대기 = (9 + 1 + 0 + 2)/4 = **3** ← 비선점보다 좋음

#### 현실적 문제: 다음 burst 길이를 미리 알 수 없다

과거 기록으로 **예측(estimate)**할 수밖에 없습니다. 방법: **exponential averaging (지수 평균)**

$$\tau_{n+1} = \alpha \cdot t_n + (1-\alpha) \cdot \tau_n$$

- $t_n$ = 실제 측정된 $n$번째 CPU burst 길이
- $\tau_{n+1}$ = 다음 burst의 예측값
- $\alpha$ ($0 \le \alpha \le 1$) = 최근 기록을 얼마나 반영할지 정하는 가중치

<details>
<summary>💡 극단값으로 이해하는 지수 평균</summary>

- **α = 0**: $\tau_{n+1} = \tau_n$. 최근 실제 기록을 아예 무시 (recent history does not count)
- **α = 1**: $\tau_{n+1} = t_n$. 바로 직전 실제 기록만 사용 (only the actual last burst counts)
- 식을 펼쳐보면 과거로 갈수록 $(1-\alpha)$가 계속 곱해져서 **오래된 기록일수록 가중치가 지수적으로 작아짐** → 그래서 "지수" 평균

</details>

---

### 3-3. Priority Scheduling (우선순위 스케줄링)

각 프로세스에 우선순위 숫자(정수)를 부여하고, **우선순위가 가장 높은** 프로세스에게 CPU 할당.

- 관례: **작은 숫자 = 높은 우선순위** (smallest integer ≡ highest priority)
- Preemptive: 더 높은 우선순위가 ready queue에 들어오면 뺏음 / Nonpreemptive: 시작하면 안 뺏김
- **SJF도 사실 priority scheduling의 일종** — "예측된 다음 CPU burst 시간"을 우선순위로 쓰는 것

| 문제 | 해결책 |
| --- | --- |
| **Starvation (기아)**: 우선순위 낮은 프로세스는 영원히 실행 못 할 수 있음 | **Aging (에이징)**: 오래 기다린 프로세스의 우선순위를 시간이 지날수록 조금씩 올려줌 |

---

### 3-4. Round Robin (RR)

각 프로세스에게 **time quantum**(시간 할당량, 보통 10~100ms)만큼만 CPU를 주고, 시간이 다 되면 **선점**해서 ready queue의 **맨 뒤로** 보냄. 돌아가면서 공평하게.

프로세스 $n$개, quantum $q$ → 각자 CPU의 $1/n$을 최대 $q$ 단위씩 나눠 받음. 어떤 프로세스도 **$(n-1)q$ 이상 기다리지 않음** → starvation 없음!

| q 크기 | 결과 |
| --- | --- |
| **q가 크면** | 사실상 FIFO(FCFS)와 같아짐 (아무도 시간 안에 안 잘리니까) |
| **q가 작으면** | 응답성은 좋지만 context switch가 너무 자주 일어나서 **오버헤드 폭증** |

> 💡 **Rule of thumb**: quantum은 **작업의 90%가 한 quantum 안에 끝날 정도**의 크기가 적당

#### 예시 (quantum = 20)

P1(53), P2(17), P3(68), P4(24)

```
| P1 | P2 | P3 | P4 | P1 | P3 | P4 | P1 | P3 | P3 |
0    20   37   57   77   97   117  121  134  154  162
```
P2는 17만에 끝나므로 quantum을 다 안 쓰고 종료 (37에서 끝)

> 특징: SJF보다 평균 **turnaround는 나쁘지만 response는 좋음**. 모든 프로세스가 빠르게 첫 CPU 할당을 받기 때문이며, 대화형(interactive) 시스템에 적합한 이유.

---

### 3-5. Multilevel Queue (다단계 큐)

Ready queue를 **여러 개의 큐로 분할**하고, 프로세스 성격에 따라 배정. 각 큐는 **자기만의 스케줄링 알고리즘**을 가짐.

| 큐 | 알고리즘 | 이유 |
| --- | --- | --- |
| foreground (interactive) | **RR** | 응답성 중요 |
| background (batch) | **FCFS** | 응답성 안 중요, 오버헤드 최소화 |

큐들 **사이의** 스케줄링도 필요합니다.

| 방식 | 설명 | 문제 |
| --- | --- | --- |
| **Fixed priority** | foreground를 전부 처리한 후 background 처리 | background가 계속 밀리는 **starvation 가능** |
| **Time slice** | 큐마다 CPU 시간을 배분. 예: 80%는 foreground(RR), 20%는 background(FCFS) | — |

우선순위 예시 (높음 → 낮음): system processes → interactive processes → interactive editing → batch → student processes

> ⚠️ 한계: 프로세스가 한번 큐에 배정되면 **못 옮김**. 이 경직성을 해결한 것이 다음 알고리즘.

---

### 3-6. Multilevel Feedback Queue (다단계 피드백 큐)

프로세스가 **큐 사이를 이동할 수 있는** 방식. 이 이동(feedback)으로 **aging을 구현**해서 starvation을 해결할 수 있음 — 일정 시간 CPU를 못 받은 프로세스는 위쪽(높은 우선순위) 큐로 올려줌.

이 스케줄러를 정의하는 **파라미터 5가지** (시험 포인트):

1. 큐의 개수 (number of queues)
2. 각 큐의 스케줄링 알고리즘
3. 프로세스를 **강등(demote)**시키는 시점을 정하는 방법
4. 프로세스를 **승격(upgrade)**시키는 시점을 정하는 방법
5. 프로세스가 서비스가 필요할 때 **어느 큐로 들어갈지** 정하는 방법

#### 예시 (큐 3개)

| 큐 | 알고리즘 |
| --- | --- |
| Q0 | RR, quantum = 8ms |
| Q1 | RR, quantum = 16ms |
| Q2 | FCFS |

동작: 새 작업은 Q0로 진입 → 8ms 안에 못 끝내면 Q1로 강등 → 거기서 16ms 더 받고도 못 끝내면 Q2로 강등되어 FCFS 처리.

> 💡 **직관**: 짧은 작업(대화형)은 Q0에서 빨리 끝나고, 긴 작업(CPU 위주)은 자연스럽게 아래로 내려감. 미래 burst를 예측하지 않고도 **SJF를 근사**하는 효과.

### 알고리즘 비교 요약

| 알고리즘 | 선점 여부 | 장점 | 단점/문제 | 해결책 |
| --- | --- | --- | --- | --- |
| FCFS | 비선점 | 단순, 공평 | Convoy effect | — |
| SJF | 둘 다 가능 (선점형=SRTF) | 평균 대기시간 **최소(optimal)** | burst 길이를 미리 모름, starvation | exponential averaging으로 예측 / aging |
| Priority | 둘 다 가능 | 중요한 일 먼저 | **Starvation** | **Aging** |
| RR | 선점 | 응답 시간 좋음, starvation 없음 | quantum 설정이 관건 (크면 FIFO, 작으면 오버헤드) | 90% rule of thumb |
| Multilevel Queue | 큐별 상이 | 프로세스 성격별 최적화 | 큐 이동 불가, starvation | time slice 배분 (80/20) |
| Multilevel **Feedback** Queue | 선점 | 큐 이동 가능, SJF 근사, aging 구현 | 파라미터 설계 복잡 | — |

---

## 4. 다중 프로세서 스케줄링 (Multiple-Processor Scheduling)

CPU가 여러 개면 스케줄링이 **더 복잡**해집니다.

| 방식 | 설명 |
| --- | --- |
| **Asymmetric multiprocessing** (비대칭) | **한 프로세서(master)만** 시스템 자료구조에 접근·스케줄링을 담당하고 나머지(slave)는 일만 함. 데이터 공유 문제가 없어 단순함 |
| **SMP** (Symmetric MultiProcessing) | **동질적인(homogeneous)** 프로세서들이 각자 스케줄링. 현대 대부분의 시스템 |
| **Load sharing** | 프로세서 간 작업량 균형 맞추기 (push migration / pull migration) |
| **Processor affinity** (친화성) | 프로세스를 **계속 쓰던 프로세서에** 배정하려는 성질. 그 프로세서의 캐시에 데이터가 남아있어 cache hit ratio가 높기 때문. 다른 CPU로 옮기면 캐시를 처음부터 다시 채워야 함 |

> **Hard affinity** vs **Soft affinity**: hard는 다른 프로세서로의 이동(migration)을 하지 않음을 보장, soft는 유지하려고 노력하지만 보장은 없음.

---

## 5. 실시간 스케줄링 (Real-Time Scheduling)

| 구분 | 설명 |
| --- | --- |
| **Hard real-time** | 중요한 작업을 **보장된(guaranteed) 시간 안에** 반드시 완료해야 함 |
| **Soft real-time** | 중요한 프로세스가 다른 것보다 **우선권**을 갖지만, **보장은 없음(NO guarantee)** |

---

## 6. 스레드 스케줄링 (Thread Scheduling)

- 스레드를 지원하는 시스템에서 **CPU(커널)는 kernel thread만 보고, user thread는 못 봄** → user thread는 **LWP**에 매핑되어야 실행 가능
- **LWP (Lightweight Process)** = user thread 라이브러리 입장에서의 **가상 프로세서**. 연결 구조: user thread ↔ LWP ↔ kernel thread

| 구분 | 결정 주체 | 영역 |
| --- | --- | --- |
| **Local Scheduling** | threads library가 어떤 user thread를 LWP에 올릴지 결정 | user 영역 |
| **Global Scheduling** | kernel이 어떤 kernel thread를 다음에 실행할지 결정 | kernel 영역 |

---

## 7. Operating System Examples

| OS | 특징 |
| --- | --- |
| **Solaris 2** | 클래스별로 별도의 큐 운영: real time > system > interactive & time-sharing. Dispatch table 특징: **우선순위와 time quantum이 반비례** — 우선순위 높음 = quantum 짧음 → interactive에겐 좋은 response time, CPU-bound에겐 좋은 throughput. I/O 처리 후 복귀 시 우선순위를 확 올려줌 |
| **Windows XP** | **Priority class**(real-time, high, above normal, normal, below normal, idle) × 클래스 내 **relative priority**(time-critical ~ idle)의 2차원 테이블로 최종 우선순위 결정 |
| **Linux** (구버전 O(1) 이전 스케줄러) | 두 알고리즘: time-sharing(**prioritized credit-based** — credit 최다 프로세스 실행, 타이머마다 credit 차감, 전부 0 되면 recrediting) / real-time(soft, Posix.1b 호환, FCFS·RR, 우선순위 0(highest, quantum 200ms)~140(lowest, quantum 10ms) — **높은 우선순위 = 긴 quantum**으로 Solaris와 반대 방향) |

---

## 8. 심화: 알고리즘 평가 방법 (Algorithm Evaluation)

스케줄링 알고리즘의 성능을 비교·평가하는 3가지 방법입니다.

| 방법 | 설명 | 특징 |
| --- | --- | --- |
| **Deterministic modeling** | 미리 정해진 특정 workload를 넣고 각 알고리즘의 성능을 계산 (앞서 한 Gantt chart 계산이 바로 이것) | 간단하지만 그 workload에서만 유효 |
| **Queuing model** | 수학(확률/통계)으로 분석 | 아래 Little's Formula 참고 |
| **Simulation model** | 각 알고리즘을 소프트웨어 시뮬레이터로 구현하고, 실제 시스템에서 수집한 데이터(trace tape)를 전부 입력해 결과를 수집·비교 | 가장 정확하지만 비용이 큼 |

#### Little's Formula

$$n = \lambda \times W$$

- $n$ = 평균 큐 길이 (Ave. queue length)
- $\lambda$ = 평균 도착률 (avg. arrival rate, 개/초)
- $W$ = 큐에서의 평균 대기 시간

셋 중 둘을 알면 나머지 하나를 구할 수 있습니다 ($\lambda = n / W$).

---

## 💡 핵심 정리

| 구분 | 내용 |
| --- | --- |
| 스케줄링이 필요한 이유 | I/O 기다리는 동안 CPU를 놀리지 않기 위해 (CPU utilization ↑) |
| Preemptive vs Nonpreemptive | 4가지 상태 전환 중 1·4번만 = 비선점, 2·3번 포함 = 선점 |
| 평가 기준 5가지 | 각각의 max/min 방향, 특히 **response time = 첫 반응까지** (완료 아님) |
| SJF | optimal이지만 미래를 알 수 없어 **예측(지수 평균)**이 필요 |
| Starvation ↔ Aging | 우선순위 계열 알고리즘의 고질병과 그 해법 |
| RR의 quantum | 크면 FCFS화, 작으면 context switch 오버헤드 |
| MLFQ | 큐 분할 + 큐별 알고리즘 + 피드백(이동) = 예측 없이 SJF 흉내 + aging으로 starvation 해결 |
| Little's formula | $n = \lambda W$ |
