Multi-Server Verifiable Computation of Low-Degree Polynomials

**Abstract:**

The conflicts between input privacy and efficiency in single-server non-interactive verifiable computation (NIVC) makes it interesting to consider the multi-server models of NIVC. Although the existing multi-server NIVC schemes provide meaningful improvements, they either require the servers to communicate or leave the client’s data unprotected. It has been an open problem to design multi-server NIVC with both input privacy and non-communicating servers. In this paper we define a multi-server verifiable computation (MSVC) model where the client secret-shares its input x among non-communicating servers, each server locally computes a function F to get a partial result, and finally the client reconstructs F(x) from all partial results. We construct five MSVC schemes for outsourcing low-degree polynomials and thus answer the open question for such polynomials. Our schemes are t-private such that any t servers learn no information about x. Our schemes are t-secure such that any t servers cannot persuade the client to output wrong results. The privacy and security can be either information-theoretic or computational. Comparing with the existing schemes, our servers can be at least two orders faster.

## Introduction

Outsourcing computation has been very popular in recent years due to the prevalence of cloud computing and the proliferation of mobile devices. It allows computationally weak devices to offload heavy computations to powerful cloud servers in a scalable pay-per-use manner. The outsourced computations are usually modeled as evaluating a function *F* at an input *x*. There are two fundamental security concerns in outsourcing computation: (1) The servers may be malicious or malfunctioning and return incorrect results. (2) The servers may be curious about the client’s data (e.g., input *x*) and abuse it. In [89], the problems of verifying the correctness of the server’s computation and protecting the client’s data have been termed as the *computation integrity* problem and the *data confidentiality* problem, respectively.

In the theoretical community, solutions to the computation integrity problem date back to as early as the interactive proofs of [6], [56] and the efficient arguments of [69], [70]. Goldwasser et al. [57] constructed interactive proofs that are suitable for outsourcing the computation of log-space uniform boolean circuits. In particular, for circuits of depth *d* and input length *n*, the prover is efficient and runs in time poly $(n)$ and the verifier is super-efficient and runs in time $(n+d)$. polylog $(n)$ and space $O(\log n)$. They found it very interesting to outsource computations with a *non-interactive* or single-round scheme, where the server sends at most one message to the client, and extended their result to a non-interactive argument for a more restricted class of functions. Such schemes are particularly interesting because the client can farm out computations without preserving active connections to servers and later the result can be returned via email with a fully written down “certificate” of correctness.

Since [57], theoretical research in the field of outsourcing computation has largely focused on non-interactive schemes for ensuring computation integrity and results in many different models [19], [30], [51], [52], [59]. We are mostly interested in the non-interactive *verifiable computation* (NIVC) model of Gennaro et al. [51]. This single-server model consists of two phases [4]: an *offline phase*, where the client sends an encoding of *F* to the server; and an *online phase*, where the client sends an encoding of *x* to the server, the server replies with an encoding of $F(x)$, and the client performs verification and reconstructs $F(x)$. The client’s offline computation is executed only once and the cost can be *amortized* over many evaluations of *F*. The client’s online computation should be *substantially faster* than the *native computation* of $F(x)$. The server’s computation should be as fast as possible.

There are two different lines in the study of single-server NIVC. One of the lines [4], [38], [51] focuses on the outsourcing of *generic* functions such as any boolean circuits. These schemes provide not only computation integrity but also *input privacy*. However, the client/server’s computations in these schemes are quite *impractical* due to their dependance on the expensive cryptographic primitives such as fully homomorphic encryptions (FHEs) and garbled circuits (GCs). The other line [17], [46], [48], [49], [80] focuses on more efficient schemes that are free of FHEs and GCs, at the price of *sacrificing input privacy* or the *generality* of functions.

In a single-server NIVC for outsourcing generic functions, it is quite challenging to achieve both input privacy and high efficiency. In fact, both Ananth et al. [2] and Schoenmakers et al. [81] believe that some form of FHE is inherently required by such schemes: in order to keep *x* private, the encoding of *x* in single-server NIVC can be viewed as an “encryption” of *x* and the encoding of $F(x)$, which is computed with *F* and the “encryption” of *x*, must allow the recovery of $F(x)$. The question is still challenging even if we consider the outsourcing of specific functions. The client may have to send an SHE (somewhat homomorphic encryption) ciphertext of *x* to the server and the server has to perform many expensive public-key operations to generate an encoding of $F(x)$.

A number of recent works have resorted to multiple servers, in order to resolve the conflicts between input privacy and efficiency in NIVC. Canetti et al. [27], [28] constructed multi-server schemes where the client’s input is always given to the server *in clear*. Ananth et. al. [2] constructed multi-server schemes for outsourcing boolean circuits, where the client’s input is hidden with the expensive primitive of *GCs*. Their schemes require *sequential communications*: the client sends a message to the first server; from the second server on, each server receives a message from the previous server and sends a message to the next server, and the last server sends a message to the client. By distributing Pinocchio [79] to three (or more) servers, Schoenmakers et al. [81] constructed schemes for outsourcing any boolean/arithmetic circuits, where the client’s input is information-theoretically private from each server. In Trinocchio [81], the servers run an MPC protocol to evaluate *F* and the MPC requires the servers to *communicate with each other*; the security relies on *non-falsifiable* assumptions [54].

Therefore, if we restrict our attention to NIVC schemes that use multiple servers, the state of the art offers protocols that either provide no input privacy (e.g. [27], [28]) or require the servers to communicate with each other (e.g. [2], [81]). In this paper, we are interested in *efficient multi-server* schemes that achieve *input privacy* with *non-communicating* servers.

*Input privacy* is extremely important and enables the client to save time by outsourcing computations, even if the input is sensitive [81] and the servers are not expected to learn any partial information. *Free of server communications* is also an important feature, from which both the cloud servers and the client may benefit. Communications among servers require the servers to operate sequentially. Very complex coordinations among servers may be needed and therefore significantly diminish the efficiency of the entire system, especially when the servers belong to competing cloud services. In multi-server NIVC schemes, the privacy of the client’s input usually relies on the assumption that servers do not collude with each other (e.g., [81]). Requiring servers to communicate means that each server may know which other servers are working on the same computation and facilitate them to collude. Without server communications, it may be possible for the client to keep the leased servers anonymous and thus reduce the potential threat. In fact, Ananth et al. [2] put forward a very interesting open question of constructing schemes where the client sends a single message to each of the servers and receives a single message from each of the servers, and can obtain the correct result from this (i.e., a model in which the servers do not communicate with each other at all).

### A. Our Contributions

We answer the open question of [2] for outsourcing low-degree multivariate polynomials, which may have many interesting applications (see Section I-B). We propose a new multi-server verifiable computation (MSVC) model. In our model, a *k*-server verifiable computation is a protocol between a *client* and *k* *servers*. In such a protocol, the client distributes both a share of the function *F* and a share of the input *x* to each server; each server locally computes a partial result; and finally the client performs verification and reconstructs $F(x)$ from all servers’ partial results. An MSVC scheme is *t*-secure if no *t* servers can persuade the client to output a wrong result. Both information-theoretic (i.t.) security and computational security will be considered. An MSVC scheme is *t*-private if no *t* servers can distinguish between two inputs. We shall consider information-theoretic privacy. We construct five schemes (see Table I) for securely outsourcing $\mathcal{P}(q,m,d)$, the set of polynomials that have coefficients in a finite field $\mathbb{F}_{q},m$ variables, and total degree $\leq d$.

![image-20230506213910277](C:\Users\lcp\AppData\Roaming\Typora\typora-user-images\image-20230506213910277.png)

**Input privacy & security.** The client’s input *x* is information-theoretically *t*-private in all schemes. The schemes $\Pi_{1},\Pi_{2}$, and $\Pi_{3}$ also have information-theoretic *t*-security; $\Pi_{4}$ and $\Pi_{5}$ are computationally *t*-secure under the DHI assumption [30] and the DLog assumption, respectively.

**Delegatability.** In all schemes, any client can prepare its input *x*, verify the servers’ partial results and reconstruct $F(x)$, i.e., all schemes are publicly delegatable [80].

**Verifiability.** The schemes $\Pi_{1},\Pi_{2}$ and $\Pi_{3}$ are privately verifiable such that only the client that prepared *x* is able to verify the servers’ partial results. $\Pi_{4}$ and $\Pi_{5}$ are publicly verifiable such that any third party can verify.

**Outsourceability.** The client in $\Pi_{1}$ and $\Pi_{4}$ has to perform an offline preprocessing of *F*. We follow the amortized model of [51], [79], [80] and call an MSVC outsourceable if the client’s online computation is substantially faster than the native computation of $F(x)$. Our schemes are all outsourceable.

**Server’s computation.** In all schemes, the computation of each server is very efficient and roughly equivalent to evaluating the outsourced function *F* once.

**Number of servers.** To outsource degree- *d* polynomials with *t*-privacy, $\Pi_{3}$ requires $dt+1$ servers. This number of servers is optimal/least provided that information-theoretic *t*-privacy is needed and the servers do not communicate. In fact, such MSVC implies a *t*-private *k*-player *d*-multiplicative secret sharing [12], which exists if and only if $k\gt dt$.

**Limitations.** The number of servers required by our schemes is linear in the degree *d* of the outsourced polynomial. A large *d* may result in a large number of servers. Therefore, although our schemes can handle polynomials of any degree, for sake of practicality they are more suitable for outsourcing low-degree polynomials, which have interesting applications.

### B. Applications

**Curve fitting on private data points.** Curve fitting [5] is the process of constructing a curve or function $y=f(x)$ that has the best fit to a set of data points $\{(x_{i}, y_{i})\}_{i=1}^{m}$. Depending on whether the data points exhibit a significant degree of error, curve fitting may be realized with *least squares regression* [34] or *interpolation* [34]. In least squares regression, to fit a degree-d polynomial, one has to evaluate polynomials in $\{(x_{i}, y_{i})\}_{i=1}^{m}$, whose degrees are determined by *d*. Small values of *d* are usually preferred and approached first [58]. Thus, low-degree polynomial evaluations will be frequently used. The same situation occurs in multiple linear regression and interpolation. When the data points contain highly sensitive personal information [65], [77], our schemes provide solutions that are both private and secure.

**Private information retrieval.** In a *t*-private *k*-server PIR, each server stores a database $F=(F_{1},F_{2},\ldots,F_{n})$; by sending a query to each server and learn the servers’ answers, the client can reconstruct a block *Fi* of its choice but any *t* servers cannot learn *i*. The *t*-private *t*-*Byzantine robust* *k*-server PIR schemes of [15], [73] allow the client to reconstruct correctly, even if *t* servers respond incorrectly. They also enable the *identification* of cheating servers. For general *t*, the best such schemes to date have communication complexity $O(n^{1/(\lfloor(2k-1)/t\rfloor-4)})$. Identification of cheating servers may be overly strong in many scenarios such as private media browsing [62], where it suffices for the client to detect the existence of cheating. Our MSVC gives *t*-private *k*-server PIR with *private (*resp. *public) detection* of cheating and *better* communication complexity of $O(n^{1/\lfloor(2k-1)/t\rfloor})$ (resp. $O(n^{1/\nu(k,t)})$, where $\nu\left(k,t\right)=\max\left\{\lfloor\frac{2k-1}{t+1}\rfloor,\lfloor\frac{2k-1}{t}\rfloor-1\right\}$). Comparing with PIR for honest servers, our schemes incur *limited extra cost*.

**Privacy preserving statics and GAS.** Computations of many meaningful statistics such as average, variance, covariance, RMS, correlation coefficient and many more, and the Genetic Risk Scores in GAS (Genetic Association Studies) [10] heavily depend on the low-degree multivariate polynomial evaluations. Our schemes provide secure outsourcing solutions that preserve the privacy of sensitive data.

### C. Efficiency

Our schemes are all outsourceable in the amortized model of [51], [79], [80]. Nevertheless, we focus on the more realistic metrics of performance such as the client’s *time cost* and *monetary cost* in MSVC. The time cost is the total time spent by the client in preparing input, waiting for the latest answer from a server, and verifying the results. The monetary cost is measured with the equivalent local computing cost of the input preparation, the result verification, and all servers’ computations. We test the proposed schemes by evaluating polynomials of degree 2, 4, 8, 16, 32 and 64. The experimental results show that the ratio of the client’s time cost to the time of locally computing $F(\mathbf{x})$ tends to the ratio of the computing speed of the client to that of each server; and the ratio of the client’s monetary cost to the cost of locally computing $F(\mathbf{x})$ tends to a multiple of the previous ratio, where the multiple is roughly equal to the number of servers. In the schemes with preprocessing (i.e., $\Pi_{1}$ and $\Pi_{4}$), the client will benefit only if the same function is evaluated multiple times. In our experiments for $d=2$, this number is $\leq 20$.

### D. Our Techniques

*t*-**Privacy.** In our constructions, *t*-privacy is achieved by secret-sharing the input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ among all servers using Shamir’s threshold scheme [87] for vectors. The client chooses a random degree-*t* curve $c(u)$ that passes through $(0,\mathbf{x})$ and distributes $k\gt dt$ curve points to *k* servers; the *k* servers provide *k* evaluations of *F*; finally the client interpolates $\phi(u)=F(c(u))$ and learns $F(\mathbf{x})=\phi(0)$.

*t*-**Security.** The verification techniques in five schemes are different. In $\Pi_{1}$, the client picks a random line $\ell(u)$ and makes both the line and the restriction of *F* on the line (i.e., $f(u)=F(\ell(u)))$ public. It also increases the degree of $c(u)$ by 1 such that $c(u)$ intersects $\ell(u)$ at a random point. With this choice, $k=d(t+1)+1$ evaluations of *F* suffice to recover $\phi(u)$. For verifications, it evaluates *F* at the random point in two different ways: one is with $\phi(u)$ and the other is with $f(u)$. It accepts only if two results agree. The main idea is leaving the heavy computation of $f(u)$ to the offline phase such that the client is able to quickly retrieve the value for verification in the online phase.

In $\Pi_{2}$, the client additionally secret-shares a random field element $\alpha$ among the servers, by using a degree- *t* univariate polynomial $b(u)$. Each server is giving a point on $c(u)$ and a share of $\alpha$, which is an evaluation of $b(u)$. The client offloads the computation of $F(\mathbf{x})$ and $\alpha F(\mathbf{x})$ to $k=(d+1)t+1$ servers. This is done by every server providing both a share of $F(\mathbf{x})$ and a share of $\alpha F(\mathbf{x})$ such that the client is able to recover $\phi(u)=F(c(u))$ and $\psi(u)=F(c(u))b(u)$. If the free terms of both polynomials differ by a factor $\alpha$, then the client believes that $\phi(u)$ is correct. Without knowing $\alpha$, any *t* servers can cheat successfully only with a very small probability.

In $\Pi_{3}$, the client will choose $c(u)$ such that it passes through through $(\alpha,\mathbf{x})$ for a random field element $\alpha$. It makes a critical change by choosing both the coefficients of $c(u)$ and $\alpha$ from an extension field of $\mathbb{F}_{q}$ such as $\mathbb{F}_{q^{2}}$, instead of $\mathbb{F}_{q}$. Then any $k=dt+1$ evaluations of *F* suffice to recover $\phi(u)=F(c(u))$ and give $F(\mathbf{x})=\phi(\alpha)$. Without knowing $\alpha$, the wrong partial results provided by any *t* servers will result in the client reconstructing a value in $\mathbb{F}_{q^{2}}\backslash\mathbb{F}_{q}$ with overwhelming probability and be rejected by the client.

$\Pi_{4}$ is obtained from $\Pi_{1}$ by converting the private verification to public. In $\Pi_{1}$, the client has to memorize the locations where $c(u)$ and $\ell(u)$ intersect. This leads to a private verification that compares the evaluations of two polynomials, i.e., $\phi(u)$ and $f(u)$. Given a cyclic group $\mathbb{G}=\langle g\rangle$ of order *q*, we publish the exponentiation of two locations and thus move the comparison to the exponent of *g*. We prove the scheme is secure under the *DHI assumption* in $\mathbb{G}$.

The scheme $\Pi_{5}$ is obtained from $\Pi_{2}$ with a public key $g^{\alpha}$. The verification is moved to the exponent of *g* as checking whether $(g^{\alpha})^{F(\mathbf{x})}=g^{\alpha F(\mathbf{x})}$. We prove the scheme is secure under the *DLog assumption* in $\mathbb{G}$.



### E. Related Work

In the security community, correctness of outsourced computations may be verified with replication, audit, or secure co-processors. The replication based solutions [3], [29], [63], [68] may be not viable and provide no input privacy. The audit-based solutions [16], [78], [82] require recalculation of the server’s work or knowledge of the server’s hardware. The secure co-processors [90], [95] are either poor in physical tamper resistance or expensive. In the cryptographic community, expensive cryptographic operations had been outsourced to semi-trusted devices [35].

**Interactive proofs.** Interactive proofs [6], [56] allow a powerful prover to convince a weak verifier of the truth of statements that could be too complex to be computed by the verifier. The statement may take the form ${F}({x})={y}$. Early research in this field focused on how to use limited resources to verify complex statements. The IP=PSPACE theorem of [75], [88] shows that polynomial space computations is verifiable in polynomial time. It was scaled down in [50] for a superpolynomial time prover. For NC circuits, Goldwasser et al. [57] proposed interactive proofs where the prover runs in polynomial time and the verifier runs in nearly linear time. The protocol of [57] has been refined and implemented in [39], [91], [93]. For NC circuits, these systems do not require preprocessing, have a highly efficient verifier, achieve low overhead for the prover, and have information-theoretic security. However, they are *interactive* and leave the client’s input *unprotected*.

**Interactive arguments.** The MIP=NEXP theorem of [8] constructed multi-prover interactive proofs with a polynomial time prover and a polylogarithmic time verifier, and led to the notion of probabilistically checkable proofs (PCP) [7]. Although a few locations of PCP suffice for verification, PCP is too long and infeasible for the verifier to process. Kilian [69], [70] suggested the prover send a Merkle tree based commitment of PCP and then interactively open the verifier-requested locations. Kilian’s idea results in interactive arguments [24] where the soundness holds for computationally bound provers. Ishai et al. [67] simplified the PCP by using a homomorphic encryption based technique. The simplified PCP was refined and implemented in [25], [84]–[86]. These systems can outsource generic functions, require a preprocessing for the verifier and have high prover overhead. These *interactive* systems provide *no input privacy*.

**Succinct non-interactive arguments of knowledge.** The non-interactive argument system of Micali [76] removed the interactions in [69], [70] by applying a random oracle to the commitment and then using the output to choose the locations that will be opened. More efficient non-interactive argument systems [19], [52], [60], [61], [79] were based on the CRS model and called succinct non-interactive arguments (SNARGs). SNARGs were then strengthened to succinct non-interactive arguments of knowledge (SNARKs) [19], [45], [52], [61], [79] such that the prover producing a convincing proof must “know” a witness. Recently many efficient SNARKs implementations [36], [83] have been proposed. An updated survey for both interactive proofs and SNARKs can be found in Thaler [92]. Most of the SNARKs require non-falsifiable assumptions [54], though some of the recent works such as [61] allows one to sidestep non-falsifiable assumptions by using stronger computational models. SNARKs in general provide *no input privacy*.

**Homomorphic authenticators (HAs).** HAs [1], [21], [22], [30], [32], [53] allow a client to compute authenticators for the elements of a dataset $\mathbf{x}=(x_{1}, x_{2}, \ldots, x_{m})$ such that the server is able to generate authenticator for a computation $F(\mathbf{x})$. The verification however is as heavy as the native computation. Catalano et al. [31], [33] constructed outsourceable HAs with multilinear maps. By considering multiple datasets, Backes et al. [9] and Gorbunov et al. [59] constructed HAs that have efficient amortized verification. None of them keeps data private. Fiore et al. [49] constructed HAs for encrypted data that can only compute quadratic polynomials.

**Multiplicative secret sharing (MSS).** In MSS [12], a client secret-shares the data $\mathbf{x}=(x_{1}, x_{2}, \ldots, x_{d})$ among multiple servers; each server can locally compute a partial result such that all partial results sum to $\prod_{i=1}^{d}x_{i}$. Verifiably MSS (VMSS) [96] additionally enables the client to verify the servers’ partial results and achieves information-theoretic privacy and security. However, it only allows the computation of *monomials*.

**Homomorphic secret sharing (HSS).** HSS [23], [74] allows a client to secret-share the data $\mathbf{x}$ among servers and then offload the computation of $F(\mathbf{x})$ to servers. HSS provides *computational* input privacy and *no verifiability*.

**Multiparty computation.** In the client-servers setting, Barkol et al. [11] constructed MPC protocols that allow a client to privately compute constant-depth circuits, but *without verification*. Dachman-Soled et al. [42] proposed an MPC protocol for computing multivariate polynomials where parties hold different *variables* as private inputs. The work of making MPC practical has been successful [72]. However, the primary focus of MPC is not outsourcing and each party’s computation is typically *as heavy as the native computation*.

**Multi-prover interactive proofs.** The multi-prover interactive proofs (arguments) [20], [71] are quite efficient. However, they are *interactive* and leave the client’s input *unprotected*.

**Verifiable secret sharing (VSS).** In VSS [26], [47], the adversary can completely dictate the behavior of the participants under its control and may also control the dealer. The verifiability of VSS guarantees that even if the dealer is corrupted, it still has consistently shared some value among the participants and the same value is later reconstructed. In our MSVC, the client may be considered as a dealer that shares **x** among the servers (participants). There are four fundamental differences between MSVC and VSS. First, the client in MSVC is never cheating. Second, the servers in MSVC never directly reconstruct **x**. Instead, each server locally computes a partial result with its share. The client is responsible to reconstruct ${F}(\mathbf{x})$ from partial results. Third, although VSS is applicable to MPC, the resulting protocols require communications among the parties. Fourth, MSVC allows the detection of cheating but provides no guarantee of reconstructing a unique value.

**Verifiable PIR.** In the *verifiable* PIR protocols of [97], [98], any server that provided a wrong answer will be identified. Although these protocols use no additional servers, both the client and the servers in these protocols have to do a lot of public-key operations. For example, in the protocols of [97], [98], the client has to spend thousands of seconds in retrieving an item from a database of *216* items. In contrast, our client and severs only need tens of milliseconds and tens of seconds respectively to retrieve an item from a database of *106* items.

**Trinocchio.** Comparing with [81], our schemes are free of server communications and non-falsifiable assumptions. Trinocchio [81] has a case study of multivariate polynomial evaluations (a medium case and a large case). We do the same case studies with $\Pi_{4}$ and $\Pi_{5}$, which share the same properties of public delegation/verification with [81]. The experimental results (Table II) show that the clients in our schemes may be slightly slower but the servers are at least two orders faster. Moreover, $\Pi_{5}$ is free of a heavy preprocessing of *F*, which is needed by Trinocchio. Trinocchio represents any function as an arithmetic circuit and the servers need to perform a large number of exponentiations, which result in worse performance. The number of required servers in MSVC depends on the degree of *F*. However, this is a theoretical consequence of information-theoretic privacy plus non-communicating servers. In [81], the servers have to interactively run MPC to evaluate ${F}(\mathbf{x})$. The same number of servers would be needed if the Trinocchio servers are not allowed to communicate.

**TABLE II** The Running Time of the Client and the Total Running Time of All Servers (Seconds)$T_{\mathrm{client}}$$T_{\mathrm{servers}}$

![Table II- The Running Time $T_{\mathrm{client}}$ of the Client and the Total Running Time $T_{\mathrm{servers}}$ of All Servers (Seconds)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang.t2-131600b499-small.gif)

## Multi-Server Verifiable Computation

For any set *S*, we denote with “$s\leftarrow S$” the procedure of choosing an element *s* uniformly from *S*. For an algorithm $\mathcal{A}$, we denote with “$y\leftarrow\mathcal{A}(x)$” the procedure of running $\mathcal{A}$ on an input *x* and assigning its output to *y*. For any integer $k\gt0$, we denote $[k]=\{1,2,\ldots,k\}$. For any set *T* and any vector $x,x_{T}$ will stand for the subvector $(x_{i})_{i\in T}$.

A *k*-server verifiable computation scheme is a protocol between a client and *k* servers $\mathcal{S}_{1},\ldots,\mathcal{S}_{k}$. In such a protocol, the client provides both a share of the input *x* and a share of the function *F* to each server; each server computes a partial result; and finally the client recovers $F(x)$ from the *k* partial results. Let $\mathcal{F}$ be a set of functions. A *k*-server verifiable computation scheme $\Pi=$ (KeyGen, ProbGen, Compute, Verify) for $\mathcal{F}$ consists of the following algorithms:



- $(\mathrm{pk}_{F},\mathrm{vk}_{F},\{\rho_{i}\}_{i=1}^{k})\leftarrow{KeyGen}(\lambda,F)$*: The key generation algorithm takes a security parameter $\lambda$ and any function $F\in\mathcal{F}$ as input and generates a value pk\*F*, which will be used by the client to prepare its input $x\in{Dom}(F),k$ function shares $\rho_{1},\ldots,\rho_{k}$, which will be used by the servers to compute partial results, and a value vk\*F*, which will be used by the client to perform verifications.*
- $(\mathrm{vk}_{x},\{\sigma_{i}\}_{i=1}^{k})\leftarrow{ProbGen}(\mathrm{pk}_{F},x)$*: The problem generation algorithm uses pk\*F* to encode any input $ x\in {Dom}(F)$ as \*k* input shares $\sigma_{1},\ldots,\sigma_{k}$, which will be given to the servers to compute with, and a value vk\*x*, which will used by the client to perform verifications.*
- $\pi_{i}\leftarrow$ Compute $(i,\rho_{i},\sigma_{i})$: For every $i\in[k]$, the server’s algorithm computes a partial result $\pi_{i}$ with $(\rho_{i},\sigma_{i})$.
- $\{F(x),\}\leftarrow$ Verify $(\mathrm{vk}_{F},\mathrm{vk}_{x},\{\pi_{i}\}_{i=1}^{k})$: The verification algorithm uses vk*F* and vk*x* to determine if $\{\pi_{i}\}_{i=1}^{k}$ form a valid encoding of $F(x)$. If it is invalid, outputs; otherwise, reconstructs $F(x)$ from $\{\pi_{i}\}_{i=1}^{k}$.



An MSVC scheme is *publicly delegatable* if pk*F* is public such that anyone is able to run ProbGen to delegate computations. An MSVC scheme is *publicly verifiable* if both vk*F* and vk*x* are public. Otherwise, the scheme is *privately verifiable*. Public delegation/verification are generally preferred. In this paper, we will construct publicly delegatable schemes, among which some are publicly verifiable and the others are privately verifiable. In particular, all schemes have a public vk*F*.

An MSVC scheme is *correct* if KeyGen and ProbGen produce values that always enable the honest servers to compute values that will verify successfully and be converted into $F(x)$.

#### Definition 1. (Correctness): 

*An MSVC scheme $\Pi$ is said to be $\mathcal{F}$-correct if for any $F\in\mathcal{F}$, any $(\mathrm{pk}_{F},\mathrm{vk}_{F},\{\rho_{i}\}_{i=1}^{k})\leftarrow {Key}{Gen}(\lambda,F)$, any $x\in{Dom}(F)$, any $(\mathrm{vk}_{x},\{\sigma_{i}\}_{i=1}^{k})\leftarrow$ ProbGen $(\mathrm{pk}_{F},x)$, any $\{\pi_{i}\leftarrow\text{Compute}(i,\rho_{i},\sigma_{i})\}_{i=1}^{k}$, it holds that ${Pr}[{Verify}(\mathrm{vk}_{F},\mathrm{vk}_{x},\{\pi_{i}\}_{i=1}^{k})=F(x)]\geq 1-{negl}(\lambda)$.*

In MSVC, a set of colluding servers may try to persuade the client to output a wrong value with incorrect partial results. For privately verifiable schemes, security against such attacks may be defined by properly generalizing the security experiment of [17], where the adversary can make a number of trials, to our multi-server setting (see Experiment 1). While the trials are essential in [17], they are not necessary here because the vk*F* in MSVC is always public and the colluding servers can finish the trials on their own. In Experiment 1, the adversary $\mathcal{A}$ models a set $\mathcal{S}_{T}$ of colluding servers for some $T\subseteq[k]$. Given $F, \mathrm{pk}_{F}, \mathrm{vk}_{F}$ and $\mathcal{S}_{T}$’s shares of the function, $\mathcal{A}$ picks an input *x*, learns $\mathcal{S}_{T}$’s shares of the input, and then chooses a set of partial results for $\mathcal{S}_{T}$. It breaks the security of MSVC if finally the challenger accepts and reconstructs a wrong value.

SECTION Experiment 1.

## $(\text{Exp}_{\mathcal{A},\Pi}^{\mathrm{Pri}}(T,F,\lambda))$

(a) $(\mathrm{pk}_{F},\mathrm{vk}_{F},\{\rho_{i}\}_{i=1}^{k})\leftarrow{KeyGen}(\lambda,F)$;

(b) $x\leftarrow\mathcal{A}(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$;

(c) $(\mathrm{vk}_{x},\{\sigma_{i}\}_{i=1}^{k})\leftarrow{ProbGen}(\mathrm{pk}_{F},x)$;

(d) $\hat{\pi}_{T}\leftarrow\mathcal{A}(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T},x,\sigma_{T})$;

(e) $\hat{\pi}_{i}\leftarrow$ Compute $(i,\rho_{i},\sigma_{i})$ for every $i\in[k]\backslash T$;

(f) $\hat{y}\leftarrow$ Verify $(\mathrm{vk}_{F},\mathrm{vk}_{x},\{\hat{\pi}_{i}\}_{i=1}^{k})$;

(g) **if** $\hat{y}\notin\{,F(x)\}$, *output 1; otherwise, output 0.*

#### Definition 2. (Security for privately verifiable schemes): 

*For $T\subseteq[k]$ and $\epsilon\gt0$, an MSVC scheme $\Pi$ is said to be $(T,\epsilon)$ secure if for any function $F\in\mathcal{F}$, any input $x\in{Dom}(F)$, and any adversary $\mathcal{A},{Pr}[{Exp}_{\mathcal{A},\Pi}^{{Pri}}(T,F,\lambda)=1]\leq\epsilon$, where the probability is taken over the randomness of $\mathcal{A}$ and the experiment. Moreover, $\Pi$ is $(t, \epsilon)$-secure if it is $(T, \epsilon)$-secure for any set $T\subseteq[k]$ of cardinality $\leq t$.*

While Experiment 1 deals with schemes where vk*F* is public but *vkx* is private. In our publicly verifiable schemes, both *vkF* and *vk* are public. The security of such schemes may defined by generalizing the security experiment of [80] in single-server setting to our multi-server setting. The resulting experiment ${Exp}_{\mathcal{A},\Pi}^{\text{PubV}}(T,F,\lambda)$ is identical to ${Exp}_{\mathcal{A},\Pi}^{\mathrm{PriV}}(T,F,\lambda)$ (as shown in Experiment 1), except that the item (d) is replaced with $\hat{\pi}_{T}\leftarrow\mathcal{A}(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T},x,\mathrm{vk}_{x},\sigma_{T})$ such that $\mathcal{A}$ ’s strategy is also based on the public key vk*x*.

#### Definition 3. (Security for publicly verifiable schemes): 

*For $T\subseteq[k]$, an MSVC scheme $\Pi$ is T-secure if for any $F\in\mathcal{F}$, for any PPT adversary $\mathcal{A},{Pr}[{Exp}_{\mathcal{A},\Pi}^{{PubV}}(T,F,\lambda)=1]\leq$ negl $(\lambda)$, where the probability is taken over the randomness of $\mathcal{A}$ and the experiment. In particular, $\Pi$ is t-secure if it is T-secure for any set $T\subseteq[k]$ of cardinality $\leq t$.*



SECTION Procedure.

## $\Sigma_{\Pi}(T,F,X)$

$(\mathrm{pk}_{F},\mathrm{vk}_{F},\{\rho_{i}\}_{i=1}^{k})\leftarrow{KeyGen}(\lambda,F)$;

$(\mathrm{vk}_{x},\{\sigma_{i}\}_{i=1}^{k})\leftarrow{ProbGen}(\mathrm{pk}_{F},x)$;

*output* $\sigma_{T}$.

In MSVC, a set of colluding servers may try to learn the client’s input from their input shares. For any $T\subseteq[k],F\in\mathcal{F}$, and $x\in{Dom}(F),\mathcal{S}_{T}$ will learn $|T|$ input shares, which can be generated by the procedure $\sigma_{\Pi}(T,F,x)$. Informally, we say that $\Pi$ is *T*-private if no strategy of $\mathcal{S}_{T}$ is able to distinguish between two inputs.

#### Definition 4. (Input privacy): 

*For $T\subseteq[k]$, an MSVC scheme $\Pi$ is T-private if for any $F\in\mathcal{F}$, any $x^{0},x^{1}\in{Dom}(F), \sigma_{\Pi}(T,F,x^{0})$ and $\sigma_{\Pi}(T,F,x^{1})$ are identically distributed. If $\Pi$ is T-private for any $T\subseteq[k]$ of cardinality $\leq t$, then $\Pi$ is said to be t-private.*

#### Remark.

The adversaries in Definition 2 and 4 are unbounded. Thus, both the private verifiability and the input privacy are information-theoretic.

SECTION III.

## Privately Verifiable Schemes

In this section we show three privately verifiable schemes for $\mathcal{P}(q,m,d)$. Each polynomial $F(\mathbf{x})=F(x_{1},\ldots,x_{m})$ in this family has up to $n=\binom{m+d}{d}$ terms. For any *t*, we shall show *k*-server schemes that are both *t*-secure and *t*-private. The three schemes will require $d(t+1)+1,(d+1)t+1$ and $dt+1$ servers, respectively. The third scheme is optimal in terms of the number of servers; and the other two will be turned into publicly verifiable schemes in Section IV.

In all constructions, the basic idea of achieving *t*-privacy is secret-sharing the input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ among all servers using Shamir’s threshold scheme [87] for vectors. That is, the client draws a random degree- *t* curve

$$\begin{equation*}c(u)=\mathbf{x}+\mathrm{r}_{1}u+\cdots+\mathrm{r}_{t}u^{t} \tag{1}\end{equation*}$$

that resides in $\mathbb{F}_{q}^{m}$ and passes through $(0,\mathbf{x})$, and then distributes *k* curve points to *k* servers. The *k* servers return the evaluations of *F* on *k* points, which will allow the client to interpolate a degree $\leq dt$ polynomial

$$\begin{equation*}\phi(u)=F(c(u)) \tag{2}\end{equation*}$$

and then learn $F(\mathbf{x})=F(c(0))=\phi(0)$. For the sake of polynomial interpolation, we always assume that $k\lt q$ and each server $\mathcal{S}_{i}$ is associated with a field element $i\in  F_{q}$.

The three schemes are mainly different in their verification techniques, which result in different numbers of servers.

### A. The First Construction

In our first construction (Scheme 1), the client will be convinced that the $\phi(u)$ in [(2)](https://ieeexplore.ieee.org/document/#deqn2) is correct if it takes the correct value at a random point $\alpha\leftarrow\Gamma_{q}^{*}\backslash[k]$. In fact, when some of the servers’ partial results are wrong, the $\phi(u)$ interpolated from partial results will not take the correct value at a random point, except with very small probability. To verify, the client must be able to learn $\phi(\alpha)=F(c(\alpha))$, the value of *F* at a random point $\mathrm{a}=c(\alpha)$. The client could choose a in KeyGen $(\lambda, F)$ and precompute $F(\mathrm{a})$ for all future verifications. However, that will require the client to memorize a, in order to choose a random curve $c(u)$ that passes through a in ProbGen $(\mathrm{pk}_{F}, \mathbf{x})$, and thus result in a scheme *without public delegation*. If the client picks a in ProbGen $(\mathrm{pk}_{F}, \mathbf{x})$ and after $c(u)$ has been chosen, then it will have to locally compute $F(\mathrm{a})$, which is as heavy as the outsourced computation.

### Scheme 1. The k-Server Scheme $\Pi_{1}(k=d(t+1)+1)$

*$KeyGen(\lambda,F)$: Choose $\ell_{0},\ell_{1}\leftarrow\mathbb{F}_{q}^{m}$, let $\ell(u)=\ell_{0}+\ell_{1}u$ and $\rho_{i}=F$ for every $i\in[k]$, compute $f(u)=F(\ell(u))$, and output $\mathrm{pk}_{F}=\ell(u),\mathrm{vk}_{F}=(\ell(u),f(u))$ and $\{\rho_{i}\}_{i=1}^{k}$.*

*${ProbGen}(\mathrm{pk}_{F},\mathbf{x})$: Choose $ a\leftarrow\mathbb{F}_{q}^{\*},\alpha\leftarrow\mathbb{F}_{q}^{\*}\backslash[k],\mathbf{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow \mathbb{F}_{q}^{m}$, let $\mathbf{a}=\ell\left(a\right),\mathrm{r}_{t+1}=\alpha^{-t-1}\left(\mathrm{a}-\left(\mathbf{x}+\sum_{s=1}^{t}r_{s}\alpha^{s}\right)\right)$ and $c(u)=\mathbf{x}+\sum_{s=1}^{t+1}r_{s}u^{s}$, compute $\sigma_{i}=c(i)$ for every $i\in[k]$, and output $\mathrm{vk}_{\mathbf{x}}=(a,\alpha)$ and $\{\sigma_{i}\}_{i=1}^{k}$.*

*$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as \*F* and output $\pi_{i}=F(\sigma_{i})$.*

*$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\lt k$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. If $\phi(\alpha)=f(a)$, output $\phi(0)$; otherwise, output $\bot$.*

The client may bypass this difficulty by choosing a random line $\ell(u)=\ell_{0}+\ell_{1}u$ in KeyGen, precomputing the restriction of *F* on the line as $f(u)=F(\ell(u))$, and making $(\ell(u), f(u))$ public. Then the client may choose a random curve

$$\begin{equation*}c(u)=\mathbf{x}+\mathbf{r}_{1}u+\cdots+\mathbf{r}_{t}u^{t}+\mathbf{r}_{t+1}u^{t+1} \tag{3}\end{equation*}$$

that passes through $(0,\mathbf{x})$ and intersects with $\ell(u)$ at a random point $\mathbf{a}=\ell(a)=c(\alpha)$, where $a\leftarrow\mathbb{F}_{q}^{*}$ and $\alpha\leftarrow\mathbb{F}_{q}^{*}\backslash[k]$. After interpolating $\phi(u)=F(c(u))$ from the servers’ partial results, the verification may be done by checking whether $\phi(\alpha)=f(a)$. Now the client only needs to evaluate two low-degree polynomials, i.e., $\phi(u)$ and $f(u)$. The main idea of speeding up the precomputation of $F(\mathbf{a})$ and thus achieving fast verification is dividing the computation of $F(\mathbf{a})=f(a)$ into two steps: (i) computing $f(u)=F(\ell(u))$; and (ii) computing $f(a)$; and then leaving the heavier step (i) to KeyGen such that it can be amortized.

When the partial results $\{\pi_{i}\}_{i=1}^{k}$ are all correctly computed, we have that $\pi_{i}=F(c(i))$ for all $i\in[k]$. Then the polynomial $\phi(u)$ interpolated in Verify is $F(c(u))$. The correctness of $\Pi_{1}$ follows from the facts that $\phi(\alpha)=F(c(\alpha))=F(\mathbf{a})= F(\ell(a))=f(a)$ and $\phi(0)=F(c(0))=F(\mathbf{x})$. Regarding input privacy and security, we have the following:

#### Theorem 1.

*The scheme* $\Pi_{1}$ *is* *t*-*private and* $(t, \epsilon)$-*secure with* $\epsilon=\frac{d(t+1)}{q-2-d(t+1)}$ (*see Appendix* *A*).

### B. The Second Construction

In our second construction (Scheme 2), the client will use [(1)](https://ieeexplore.ieee.org/document/#deqn1) to secret-share its input among the servers and use [(2)](https://ieeexplore.ieee.org/document/#deqn2) to reconstruct the output. To add verifiability, the client will additionally secret-share a random field element $\alpha\leftarrow\Gamma_{q}^{*}$ among the *k* servers using a random polynomial

$$\begin{equation*}b(u)=\alpha+\gamma_{1}u+\cdots+\gamma_{t}u^{t} \tag{4}\end{equation*}$$

of degree $\leq t$. Each server returns not only $F(c(i))$ but also $b(i)F(c(i))$. Finally, the client interpolates two polynomials

$$\begin{equation*}\phi(u)=F(c(u))\, \psi(u)=F(c(u))b(u) \tag{5}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

of degree $\leq dt$ and $\leq(d+1)t$, respectively. The verification is done by checking whether $\psi(0)=\alpha\phi(0)$.

### Scheme 2. The k-Server Scheme $\Pi_{2}(k=(d+1)t+1)$

*${KeyGen}(\lambda,F)$: Let $\rho_{i}=F$ for all $i\in[k]$. Output $\mathrm{pk}_{F}=,\mathrm{vk}_{F}= \bot$ and $\{\rho_{i}\}_{i=1}^{k}$.*

*${ProbGen}(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q}^{\*},\mathrm{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow\mathbb{F}_{q}^{m},\gamma_{1},\ldots,\gamma_{t}\leftarrow \mathbb{F}_{q}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}u^{s}$ and $b(u)=\alpha+\sum_{s=1}^{t}\gamma_{s}u^{s}$, and compute $\sigma_{i}=(c(i),b(i))$ for all $i\in[k]$. Output $\mathrm{vk}_{\mathbf{x}}=\alpha$ and $\{\sigma_{i}\}_{i=1}^{k}$.*

*$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as \*F*, compute $v_{i}=F(c(i))$ and $w_{i}= v_{i}b(i)$, and output $\pi_{i}=(v_{i},w_{i})$.*

*$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq dt$ such that $\phi(i)=v_{i}$ for all $i\in[k]$ and a polynomial $\psi(u)$ of degree $\leq(d+1)t$ such that $\psi(i)=w_{i}$ for all $i\in[k]$. If $\psi(0)=\alpha\bar{\phi}(0)$, output $\phi(0)$; otherwise, output $\bot$.*

When the partial results $\{(v_{i}, w_{i})\}_{i=1}^{k}$ are all correctly computed, we have that $v_{i}=F(c(i))$ and $w_{i}=F(c(i))b(i)$ for all $i\in[k]$. Then the polynomials $\phi(u)$ and $\psi(u)$, which are interpolated in Verify, will satisfy [(5)](https://ieeexplore.ieee.org/document/#deqn5). The correctness of $\Pi_{2}$ then follows from the facts that $\psi(0)=\phi(0)b(0)=\alpha\phi(0)$ and $\phi(0)=F(c(0))=F(\mathbf{x})$. The cheating servers may forge a set of partial results which then result in two polynomials $\hat{\phi_{\wedge}}(u)\mathrm{and}_{\wedge}\hat{\psi}(u)$ in Verify. However, without knowing $\alpha,\psi(0)=\alpha\phi(0)$ will be false except with very small probability.

#### Theorem 2.

*The scheme* $\Pi_{2}$ *is* *t*-*private and* $(t, \epsilon)$-*secure with* $\epsilon=\frac{1}{q-1}$ (*see Appendix* *B*).

### C. The Third Construction

In our third construction (Scheme 3), the client will work over $\Gamma_{q^{2}}$. Instead of choosing a random curve $c(u)$ that passes through $(0, \mathbf{x})$, it will choose a random curve

$$\begin{equation*}c(u)=\mathbf{x}+\mathbf{r}_{1}(u-\alpha)+\cdots+\mathbf{r}_{t}(u^{t}-\alpha^{t}) \tag{6}\end{equation*}$$

that passes through $(\alpha,\mathbf{x})$ at a random point $\alpha\leftarrow\mathbb{F}_{q^{2}}^{*}\backslash[k]$. Each server $\mathcal{S}_{i}$ will be given a curve point $(i,c(i))$ such that any *t* servers learn no information about $\mathbf{x}$. Given $k=dt+1$ servers, the client would be able to reconstruct the degree $\leq dt$ polynomial $\phi(u)=F(c(u))$ and learn $F(\mathbf{x})=\phi(\alpha)\in\mathbb{F}_{q}$.

### Scheme 3. The k-Server Scheme $\Pi_{3}(k=dt+1)$

*$KeyGen(\lambda,F)$: Let $\rho_{i}=F$ for all $i\in[k]$. Output $\mathrm{pk}_{F}=,\mathrm{vk}_{F}= \bot$ and $\{\rho_{i}\}_{i=1}^{k}$.*

*$ProbGen(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q^{2}}^{\*}\backslash[k]$ and $\mathrm{r}_{1},\ldots,\mathrm{r}_{t}\leftarrow\mathbb{F}_{q^{2}}^{m}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}(u^{s}-\alpha^{s})$, set $\sigma_{i}=c(i)$ for all $i\in[k]$.*

*Output ${vk}_{\mathbf{x}}=\alpha$ and $\{\sigma_{i}\}_{i=1}^{k}$.*

*$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as \*F* and output $\pi_{i}=F(\sigma_{i})$.*

*$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq dt$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. If $\phi(\alpha)\in\mathbb{F}_{q}$, output $\bar{\phi}(\alpha)$; otherwise, output $\bot$.*

When the partial results $\{\pi_{i}\}_{i=1}^{k}$ are all correctly computed, we have $\pi_{i}=F(c(i))$ for all $i\in[k]$. Then the polynomial $\phi(u)$ interpolated in Verify is $F(c(u))$. The correctness of $\Pi_{3}$ follows from the fact that $\phi(\alpha)=F(c(\alpha))=F(\mathbf{x})\in\mathbb{F}_{q}$. The cheating servers may provide incorrect partial results, which then result in a polynomial $\hat{\phi}(u)\neq F(c(u))$ in Verify. The cheating will be detected if $\hat{\phi}(\alpha)\notin\mathbb{F}_{q}$. Therefore, $\hat{\phi}(u)$ will not allow the cheating servers to break the security unless $\hat{\phi}(\alpha)\in\mathbb{F}_{q}\backslash\{\phi(\alpha)\}$. However, without knowing $\alpha$, the last event will not occur except with very small probability.

#### Theorem 3.

*The scheme* $\Pi_{3}$ *is* *t*-*private and* $(t, \epsilon)$-*secure for* $\epsilon=\frac{(q-1)dt}{q^{2}-2-dt}$ (*see Appendix* *C*).

## Publicly Verifiable Schemes

The schemes in Section III are privately verifiable. Public verifiability allows a third party to verify as well and thus settle the possible disputes between client and servers. We shall convert $\Pi_{1}$ and $\Pi_{2}$ to schemes with public verification. The security of the new schemes is not information-theoretic but relies on cryptographic assumptions (see Appendix D).

### A. The First Construction

In $\Pi_{1}$, the client accepts the servers’ partial results if and only if $\phi(\alpha) =f(a)$, where $(a, \alpha)$ is a private key for verification. To obtain a publicly verifiable scheme (Scheme 4), we shall choose a cyclic group $\mathrm{G}=\langle g\rangle$ of prime order *q* and apply a new verification as follows:

$$\begin{equation*}g^{\phi(\alpha)}=g^{f(a)}. \tag{7}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

To enable the verification, a key of the form $\mathrm{vk}_{\mathbf{x}}=\{g^{a^{i}}\}\cup\{g^{\alpha^{i}}\}$ will be made public.

### Scheme 4. The k-Server Scheme $\Pi_{4}(k=d(t+1)+1)$

*$KeyGen(\lambda,F)$: Choose $\ell_{0},\ell_{1}\leftarrow\mathbb{F}_{q}^{m}$, let $\ell(u)=\ell_{0}+\ell_{1}u$, compute $f(u)=F(\ell(u))$; let $\rho_{i}=F$ for every $i\in[k]$; output $\mathrm{pk}_{F}= \ell(u),\mathrm{vk}_{F}=(\ell(u),f(u))$ and $\{\rho_{i}\}_{i=1}^{k}$.*

*${ProbGen}(\mathrm{pk}_{F},\mathbf{x})$: Choose $a\leftarrow\mathbb{F}_{q}^{\*},\alpha\leftarrow\mathbb{F}_{q}^{\*}\backslash[k],\mathbf{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow\mathbb{F}_{q}^{m}$, let $\mathrm{a}=\ell\left(a\right),\mathbf{r}_{t+1}=\alpha^{-t-1}\left(\mathrm{a}-\left(\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}\alpha^{s}\right)\right)$ and $c(u)= \mathbf{x}+\sum_{s=1}^{t+1}\mathbf{r}_{s}u^{s}$; choose a cyclic group $\mathbb{G}=\langle g\rangle$ of order \*q*; let $\mathrm{vk}_{\mathbf{x}}=(g,g^{a},\ldots,g^{a^{d}},g^{\alpha},\ldots,g^{\alpha^{k-1}})$; let $\sigma_{i}=c(i)$ for every $i\in[k]$; output $\mathrm{vk}_{\mathbf{x}}$ and $\{\sigma_{i}\}_{i=1}^{k}$.*

*$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as \*F* and output $\pi_{i}=F(\sigma_{i})$.*

*$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq d(t+1)$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. If $g^{\phi(\alpha)}=g^{f(a)}$, output $\phi(0)$; otherwise, output $\bot$.*

Proofs for the correctness and *t*-privacy of $\Pi_{4}$ are identical to those of $\Pi_{1}$ and omitted. Regarding security, we show a reduction from the $(k-1)$-DHI problem to the problem of satisfying [(7)](https://ieeexplore.ieee.org/document/#deqn7) with the partial results crafted by an adversary.

#### Theorem 4.

*The scheme* $\Pi_{4}$ *is* *t*-*secure under the* $(k-1)-DHI$ *assumption in* G (*see Appendix* *E*).

### B. The Second Construction

In $\Pi_{2}$, not only the client’s input $\mathbf{x}$ is secret-shared with a degree $\leq t$ curve $c(u)$ but also a random field element $\alpha$ is secret-shared using a degree $\leq t$ polynomial $b(u)$. Each server evaluates *F* on a point of the curve and returns both the result and the product of this result with a share of $\alpha$. The client then interpolates $\phi(u)=F(c(u))$ and $\psi(u)=F(c(u))b(u)$. It accepts the servers’ partial results if and only if $\psi(0)=\alpha\phi(0)$, where $\alpha$ is a private key for verification. To obtain a publicly verifiable scheme (Scheme 5), we shall choose a cyclic group $\mathrm{G}=\langle g\}$ of prime order *q* and apply the verification

$$\begin{equation*}g^{\psi(0)}=g^{\alpha\phi(0)}. \tag{8}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

To enable the verification, $\mathrm{vk}_{\mathbf{x}}=g^{\alpha}$ is made public.

### Scheme 5. The k-Server Scheme $\Pi_{5}(k=(d+1)t+1)$

${KeyGen}(\lambda,F)$: Let $\rho_{i}=F$ for every $i\in[k]$. Output $\mathrm{pk}_{F}=, \mathrm{vk}_{F}= \bot$ and $\{\rho_{i}\}_{i=1}^{k}$.

*$ProbGen(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q}^{\*},\mathrm{r}_{1},\ldots,\mathrm{r}_{t}\leftarrow\mathbb{F}_{q}^{m},\gamma_{1},\ldots,\gamma_{t}\leftarrow \mathbb{F}_{q}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}u^{s},b(u)=\alpha+\sum_{s=1}^{t}\gamma_{s}u^{s}$, and $\sigma_{i}=(c(i),b(i))$ for all $i\in[k]$. Choose a cyclic group $\mathbb{G}=\langle g\rangle$ of order \*q*. Output $\mathrm{vk}_{\mathbf{x}}=g^{\alpha}$ and $\{\sigma_{i}\}_{i=1}^{k}$.*

*$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as $F,\sigma_{i}$ as $(c(i),b(i))$, compute $v_{i}= F(c(i)),w_{i}=v_{i}b(i)$, output $\pi_{i}=(v_{i},w_{i})$.*

*$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq dt$ such that $\phi(i)=v_{i}$ for all $i\in[k]$ and a polynomial $\psi(u)$ of degree $\leq(d+1)t$ such that $\psi(i)=w_{i}$ for all $i\in[k]$. If $g^{\psi(0)}=g^{\alpha\phi(0)}$, output $\phi(0)$; otherwise, output $\bot$.*

Proofs for the correctness and *t*-privacy of $\Pi_{4}$ are exactly identical to those of $\Pi_{2}$ and omitted. Regarding security, we show a reduction from the DLog problem to the problem of satisfying [(8)](https://ieeexplore.ieee.org/document/#deqn8) with the partial results crafted by an adversary.

#### Theorem 5.

*The scheme* $\Pi_{5}$ *is* *t*-*secure under the DLog assumption in* G (*see Appendix* *F*).



## Performance Evaluation

In this section, we evaluate the performance of our schemes in Section III and IV. Our evaluation will cover not only the client’s *time cost* but also its *monetary cost*, in executing the schemes. In particular, for monetary cost, although consumptions of various computing resources could be accounted, our evaluation will be simplified and mainly base on the running times of all algorithms.

We denote respectively with $T_{\mathrm{k}}, T_{\mathrm{p}}, T_{\mathrm{c}}^{i}, T_{\mathrm{v}}$ and Tn the time needed by the client running ${KeyGen}(\lambda, F)$, the client running ${ProbGen}\left(\mathrm{pk}_{F}, \mathrm{x}\right)$, the *i*th server running Compute $\left(i, F, \sigma_{i}\right)$, the client running ${Verify}\left(\mathrm{vk}_{F}, \mathrm{vk}_{\mathrm{X}},\left\{\pi_{i}\right\}_{i=1}^{k}\right)$, and the client locally computing $F(\mathrm{x})$. Let Tc and $T_{\mathrm{c}}^{*}$ be the total and maximum running time of the servers, i.e.,

$$\begin{equation*}T_{\mathrm{c}}=\sum_{i=1}^{k} T_{\mathrm{c}}^{i} ; \quad T_{\mathrm{c}}^{*}=\max \left\{T_{\mathrm{c}}^{1}, T_{\mathrm{c}}^{2}, \ldots, T_{\mathrm{c}}^{k}\right\}. \tag{9}\end{equation*}$$



Our evaluation will focus on the following question: $(\triangle)$ *In terms of the time/monetary cost, under what conditions and to what extent the client will benefit from using MSVC?*

### A. Time Cost

Among the five schemes, $\Pi_{1}$ and $\Pi_{4}$ have a non-empty KeyGen, which is heavier than the native computation of $F(\mathrm{x})$. In all schemes, the client has to prepare its input x for computation and verify the servers’ partial results, by running ProbGen and Verify respectively.

In the field of verifiable computation, many pioneer works such as [51], [79], [80] have suggested that the one-time cost Tk of executing $KeyGen(\lambda, F)$ can be *amortized* when the same function *F* is evaluated many times, and a scheme is *outsourceable* if the total running time of ProbGen and Verify is far smaller than that of the native computation, i.e.,

$$\begin{equation*}T_{\mathrm{p}}+T_{\mathrm{v}} \ll T_{\mathrm{n}}. \tag{10}\end{equation*}$$



Following this idea, a proof for [(10)](https://ieeexplore.ieee.org/document/#deqn10) appears in Section G and shows that our schemes are all outsourceable provided that

$$\begin{equation*}k m t^{2}+k^{3}+(k+d) \lambda=o(n d), \tag{11}\end{equation*}$$



where $n=\begin{pmatrix}m+d \\ d\end{pmatrix}$. Given a security parameter $\lambda$, the parameters $(q, m, d, t)$ in our schemes may be chosen such that

$$\begin{equation*}q \approx 2^{O(\lambda)}, m={poly}(\lambda), d=O(1), t=O(1), \tag{12}\end{equation*}$$

to satisfy [(11)](https://ieeexplore.ieee.org/document/#deqn11) and thus result in outsourceable schemes.

In terms of time cost, we believe that a client may benefit from MSVC, only if the client spends less time in learning $F (\mathbf{x})$, compared with locally computing $F (\mathbf{x})$. While the requirement of [(10)](https://ieeexplore.ieee.org/document/#deqn10) is interesting and may realize this idea in a setting where the servers’ computations take essentially no time (i.e., $T_{c}=0)$, it is generally not realistic.

In MSVC, some of the servers may finish their computations earlier than the others and the client may receive their partial results earlier. However, the client will not be able to verify until all partial results arrive. If all servers initiate their computations simultaneously, the time that has to be spent by the client before actually learning $F (\mathbf{x})$ will be $T_{p}+T_{v}+T_{c}^{\ast }$, where $T_{c}^{\ast }$ is the maximum of the servers’ running time. In the amortized model of [51], [79], [80], a client will benefit from MSVC with reduced time cost if and only if

$$\begin{equation*}T_{p}+T_{v}+T_{c}^{\ast }\gt T_{n}. \tag{13}\end{equation*}$$

As per [(13)](https://ieeexplore.ieee.org/document/#deqn13), in terms of time cost, the aforementioned question $(\Delta)$ may be answered by analyzing the parameter

$$\begin{equation*}\texttt{RT}=(T_{p}+T_{v}+T_{c}^{\ast })/T_{n} \tag{14}\end{equation*}$$

the ratio of the client’s time cost in MSVC to its time cost of locally computing $F(\mathbf{x})$. In particular, the client may benefit from MSVC if and only if Rt < 1; the smaller the Rt is, the more the client will benefit from using MSVC. Ideally, we would like Rt to be as small as possible.

The actual values of Rt may depend on factors such as the scale of the problem instance $(F, \mathbf{x})$. As there is a gap between the computing speed of the client and that of every server, Rt < 1; may be easily satisfied by large problem instances. Ideally, we would like to have Rt < 1; for small problem instances as well. Given $(q, d)$, the scale of the problem $(F, \mathbf{x})$ may be measured with *m*, the number of variables in *F*. In order to answer the question of ($\triangle$), we need to decide an ${m}_{0}$ such that $\mathrm{Rt};\lt1$ for all $m\gt m_{0}$, and analyze how Rt will vary as *m* is increasing.

### B. Monetary Cost

Cloud computing aims to cut costs. Computing resources provided by the cloud servers are usually much cheaper than those owned by the client. A main motivation for the client to outsource computations is reducing the monetary cost.

By locally computing $F(\mathbf{x})$, the client has to spend time Tn and the monetary cost of this local computation may be quite high. By using MSVC, the amount of local computations is reduced to $T_{\mathrm{p}}+T_{\mathrm{v}}$. However, the client has to additionally pay for the Tc unit of cloud servers’ computations. Let Rp be the ratio of the price of the client’s local computation to that of the cloud servers’ computation. Then the client’s total monetary cost in MSVC will be equivalent to $T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}/\mathrm{Rp}$ units of its local computation. In terms of monetary cost, the client will benefit from using MSVC if and only if

$$\begin{equation*}T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}/\mathrm{Rp}\lt T_{\mathrm{n}}. \tag{15}\end{equation*}$$

As per [(15)](https://ieeexplore.ieee.org/document/#deqn15), in terms of monetary cost, the aforementioned question ($\triangle$) may be answered by analyzing the parameter

$$\begin{equation*}\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}}), \tag{16}\end{equation*}$$

the least value of Rp such that [(15)](https://ieeexplore.ieee.org/document/#deqn15) holds for all $\mathrm{Rp}\gt\mathrm{Rp}^{\star}$.

The actual values of Rp*** may depend on factors such as the scale of $(F, \mathbf{x})$. The smaller the Rp*** is, the easier it is for the client to benefit from using MSVC. We are mostly interested in the values of Rp*** when $m\gt {m}_{0}$, in order to decide when the client will benefit in terms of both the time cost and the monetary cost. More precisely, we need to analyze how Rp*** will vary as $m\gt {m}_{0}$ is increasing, and decide an upper bound, say $\mathrm{Rp}_{0}^{\star}$, of Rp*** for all $m\gt {m}_{0}$.

### C. Break-Even Points

In $\Pi_{1}$ and $\Pi_{4}$, the client has to perform a one-time execution of KeyGen$(\lambda, F)$. Although these schemes are outsourceable according to [(10)](https://ieeexplore.ieee.org/document/#deqn10) and under the condition of [(11)](https://ieeexplore.ieee.org/document/#deqn11), the client will not really benefit from using them unless the same function *F* is evaluated multiple times.

If the time Tk needed by KeyGen$(\lambda, F)$ is accounted and the function *F* is evaluated at *N* inputs, then the client’s time cost per input in MSVC will be $T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*}+T_{\mathrm{k}}/N$ and the client may benefit from using MSVC if and only if

$$\begin{equation*}T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*}+T_{\mathrm{k}}/N\lt T_{\mathrm{n}}. \tag{17}\end{equation*}$$

Given an ${m}_{0}$ from Section V-A, we call the smallest integer $N=\mathrm{Nt}$; such that [(17)](https://ieeexplore.ieee.org/document/#deqn17) holds for all $m\gt m_{0}$ a *break-even point* for the client’s time cost. On the other hand, the client’s monetary cost per input will be $T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}/\mathrm{Rp}+T_{\mathrm{k}}/N$ and the client may benefit from using MSVC if and only if

$$\begin{equation*}T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}/\mathrm{Rp}+T_{\mathrm{k}}/N\lt T_{\mathrm{n}}. \tag{18}\end{equation*}$$

Given an ${m}_{0}$ from Section V-A and an $\mathrm{Rp}_{0}^{\star}$ from Section V-B, we call the smallest integer $N=\mathrm{Nm}$ such that [(18)](https://ieeexplore.ieee.org/document/#deqn18) holds for all $m\gt m_{0}$ and $\mathrm{Rp}=\mathrm{Rp}_{0}^{\star}$ a *break-even point* for the client’s monetary cost. Ideally, we would like Nt and Nm to be as small as possible. In order to answer the question of ($\triangle$), we need to analyze how Nt; and Nm vary as well.

### D. Our Implementation

In this section, we describe our implementations in detail. The main parameters in our schemes include $\lambda, q, m, d$ and *t*, where $\lambda$ is a security parameter, $(q, m, d)$ specify a function family $\mathcal{P}(q, m, d)$, and *t* is the threshold for privacy and security. We will describe both the choices of these parameters and the choice of the cyclic group $\mathbb{G}$ in $\Pi_{4}$ and $\Pi_{5}$. We will also describe the libraries and platforms with which our experiments will be conducted.

**Security parameters, cyclic groups and finite fields.** Our implementations achieve $\lambda$-bit security for $\lambda=128$. Our privately verifiable schemes $\Pi_{1}, \Pi_{2}$ and $\Pi_{3}$ are $\epsilon$-secure for $\epsilon=O(1/q)$. In order to assure 128-bit security, a sensible choice of *q* would be $q\approx 2^{128}$. In particular, we shall choose $q=2^{128}+51$ in our implementation of these schemes. Then the servers in $\Pi_{1}$ and $\Pi_{2}$ will perform polynomial evaluations over a 129-bit finite field $\Gamma_{q}$, except in $\Pi_{3}$ where the computation is done over a 257-bit finite field $\Gamma_{q^{2}}$. The security of our publicly verifiable schemes $\Pi_{4}$ and $\Pi_{5}$ relies on the DLog assumption in a cyclic group $\mathbb{G}$ of prime order *q*. For the sake of efficiency, we choose $\mathbb{G}$ to be the ristretto255 group [43], a cyclic group of prime order $q=2^{252}+27742317777372353535851937790883648493$ that is obtained from Bernstein’s Curve25519 [18] by applying Hamburg’s Decaf point compression technique [64] for constructing prime-order groups. It is believed that such a group provides 128-bit of security strength [41]. As a result, we have that $q\approx 2^{253}$ and the servers in $\Pi_{4}$ and $\Pi_{5}$ will perform polynomial evaluations over a 253-bit finite field.

**Libraries.** The field operations in $\Gamma_{q}$ and $\Gamma_{q^{2}}$ are implemented with FLINT (v2.8.0), the fast library for number theory, which is based on GMP. The ristretto255 cyclic group $\mathbb{G}$ is implemented with libsodium (v1.0.18). The programming of all schemes is done in C.

**Platforms.** We perform each server’s computation (i.e., the Compute in MSVC) on a virtual machine VM*s* that runs a Ubuntu 20.04 operating system with a single core of a 3.60 GHz CPU and 8GB of RAM; and perform both the client’s computation (i.e., KeyGen, ProbGen and Verify) and the native computation on a virtual machine VM*c* that runs a Ubuntu 20.04 operating system with a single core of a 1.9 GHz CPU and 4GB of RAM. With these choices we try to mimic the gap of computing speed between the client’s device (e.g., a netbook) and the servers’ machines.

**Degree of polynomials.** In theory there is no limitation on the degree *d* of the polynomials that can be outsourced by MSVC. However, due to three reasons, our schemes are more suitable for outsourcing *low-degree* polynomials.



- First, the number *k* of servers required by each of our schemes is linear in *d*. A large *d* would result in a large number of servers. While $k\geq dt+1$ is necessary in order to provide information-theoretic *t*-privacy with non-communicating servers, and thus one of our schemes (i.e., $\Pi_{3})$ is already optimal in terms of the number of servers and can hardly be improved, it may be still undesirable to use too many servers. Therefore, we prefer to use the proposed MSVC to evaluate polynomials of small degree *d*, in order to limit the number of required servers.
- Second, the Theorem 8 in Appendix G shows that our schemes are outsourceable (i.e., $T_{\mathrm{p}}+T_{\mathrm{v}}\ll T_{\mathrm{n}}$), provided that [(11)](https://ieeexplore.ieee.org/document/#deqn11) is satisfied. As we are working over finite fields of order $q\approx 2^{O(\lambda)}$, many choices of $(m, d, t)$ would meet the requirement of [(11)](https://ieeexplore.ieee.org/document/#deqn11), e.g., $(m, d, t)=(\mathrm{poly}(\lambda), O(1)$, $O(1))$ or $(m, d, t) = (O(1), \mathrm{poly}(\lambda), O(1))$. Among these choices, the client will benefit most from the former one, which requires $d=O(1)$.
- Third, comparing with high-degree polynomials, the low-degree ones are preferred in many scenarios. For example, the information-theoretic PIR protocols [37], [94] require low-degree polynomial interpolations and give quite practical communication complexity; in the polynomial regression based curve fitting, low-degree polynomials are usually preferred and approached first [58], and require low-degree multivariate polynomial evaluations.



Many existing works experiment with low-degree polynomials. For example, the degree-2 and degree-3 polynomial evaluations have been the focus of the implementations of [39], [85]. Pinocchio [79] and Trinocchio [81] also experiment with polynomials of degree 6, 8 and 10. In Section V-E, we will provide a detailed analysis of the parameters Rt, Rp, Nt and Nm with the experimental results of degree-2 polynomial evaluations. However, besides *d*=2, we will also consider polynomials of degree *d* = 4, 8, 16, 32, 64 and show slightly rough analysis of the same parameters, in order to have a more complete overview of the performance.

**Threshold for privacy and security.** Our schemes in Section III and IV are both information-theoretically *t*-private and *t*-secure. Any choice of the threshold *t* may have its pros and cons. Besides privacy and security, the *t* also has direct impact on the number of required servers and the client’s computation complexity. A larger *t* not only implies stronger privacy and security, but also results in a larger number *k* of servers and a heavier workload of the client. A smaller *t* would imply less servers and faster verification, but also lower security level. How to choose *t* may not only depend on the client’s preference, but also depend on the actual situation of how many servers can collude with each other. In our model and constructions, the servers *do not need to communicate* with each other, in order for the client to reconstruct $F(\mathbf{x})$. Such model and constructions allow the client to keep every server anonymous from the others. Then it would be difficult for even two servers to collude. Thus, it seems not unreasonable to choose a small *t*. Based on these observations, we will experiment with relatively small *t* such as $t=1,2,3$.

![Fig. 1. - The value of $\mathrm{RC}=(T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*})/T_{\mathrm{n}}$ in degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang1-131600b499-small.gif)

**Fig. 1.**

The value of $\mathrm{RC}=(T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*})/T_{\mathrm{n}}$ in degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)

### E. Experimental Results: Polynomials of Degree Two

As stated in Section V-D, for degree-2 polynomial evaluations, we choose $t=1,2,3$ and choose *q* to be either a 129-bit prime or a 253-bit prime. Given *q*, the scale of a problem instance $(F,\mathbf{x})\in\mathcal{P}(q,m,2)\times\mathbb{F}_{q}^{m}$ can be measured with *m*, the number of variables in *F*. In the experiments, we choose $m=200,400,\ldots,2000$ and analyze how Rt, Rp (for all schemes) and Nt, Nm (for $\Pi_{4}$ and $\Pi_{5}$) vary as *m* is increasing. For each $(t,m)$, we run the KeyGen, ProbGen and Verify on VM*c* and run the Compute on VM*s*, respectively. We also evaluate $F(\mathbf{x})$ on VM*c*. We repeat each execution 5 times and record the average CPU time such that the standard deviations are within 5% of the means.

**Time cost.** Fig. 1 plots the experimental results of Rt; for the five MSVC schemes and for all settings of (*t, m*)$\in\{1,2,3\}\times\{200,400, 2000\}$. For $\Pi_{1}, \Pi_{2}, \Pi_{4}$ and $\Pi_{5}$, it shows that Rt < 1 starting from *m* = 200 (${m}_{0}$ = 200) and Rt tends to be < 0.6 when *m* is large. Thus, in terms of time cost, the client may benefit from using these schemes when $m\geq 200$. Furthermore, the client may bring its time cost in these schemes down to $0.6 T_{\mathrm{n}}$ when *m* is large enough. On the other hand, for $\Pi_{3}$, Fig. 1 shows that Rt > 1 for all *m* and Rt; tends to be $\approx$ 1.2 when *m* is large, which leads to a seemingly surprising conclusion that the client will not benefit from using $\Pi_{3}$. However, we will see shortly that this is not always true and whether one will benefit from $\Pi_{3}$ largely depends on the gap between the computing speed of the client and that of the servers. On one hand, when *m* is large enough, we always have that $T_{\mathrm{p}}+T_{\mathrm{v}}=o(T_{\mathrm{n}})$ in all schemes. As a result, we will roughly have $\mathrm{Rt};\approx T_{\mathrm{c}}^{*}/T_{\mathrm{n}}$. On the other hand, each server in $\Pi_{1}, \Pi_{2}, \Pi_{4}$ and $\Pi_{5}$ needs to evaluate *F* at a random point of $\mathbb{F}_{q}^{m}$ and the workload is roughly equal to that of computing $F(\mathbf{x})$. In our experiments, $T_{\mathrm{c}}^{*}$ is needed by VM*s* evaluating *F* once and Tn is needed by VM*c* evaluating $F(\mathbf{x})$. Note that the ratio of the speed of VM*c* to that of VM*s* is $1.9/3.6\approx 0.53$. Therefore, in the four schemes, given that the client and each server have the same workload, we would have that Rt $\rightarrow 0.53$ as $ m\rightarrow\infty$. This fact has been partially reflected in Fig. 1 as Rt $\leq 0.6$ starting from $m=1600$. In $\Pi_{3}$, the situation is quite different. Each server has to evaluate *F* once, but at a random point of $\mathbb{F}_{q^{2}}^{m}$. Based on the fast integer multiplication of FLINT, the workload of each server is roughly twice of that of the client. With our virtual machines, one would have that Rt $\rightarrow 1.06$ as $ m\rightarrow\infty$, which is partially reflected in Fig. 1. In order for $\mathrm{Rt}\lt1$ in $\Pi_{3}$, one requires that the ratio of the speed of VM*s* to that of VM*c* is > 2.

![Fig. 2. - The value of $\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang2-131600b499-small.gif)

**Fig. 2.**

The value of $\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)

**Monetary cost.** Fig. 2 plots the experimental results of Rp for the five schemes and for all settings of $(t, m)\in\{1,2,3\}\times\{200,400, 2000\}$. For every $t\in\{1,2,3\}$, it shows that the Rp*** value of every scheme is decreasing as *m* is increasing. In particular, when $m=2000$, the Rp*** values are as shown in Table III. Therefore, for every $t\in\{1,2,3\}$ and $\Pi\in\{\Pi_{1}, \Pi_{2}, \Pi_{3}, \Pi_{4}, \Pi_{5}\}$, as long as the ratio of the price of VM*c*’s computation to that of VM*s*’s computation is larger than the $(t, \Pi)$-entry of Table III, the client will benefit in terms of monetary cost by using MSVC to evaluate a 2000-variate polynomial of degree 2. When $ m\rightarrow\infty$, we expect the Rp*** values of every scheme to converge. When *m* is large enough, we have that $T_{\mathrm{p}}+T_{\mathrm{v}}=o(T_{\mathrm{n}})$ and thus

$$\begin{equation*}\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}})\approx T_{\mathrm{c}}/T_{\mathrm{n}}\approx kT_{\mathrm{c}}^{*}/T_{\mathrm{n}}\approx k\cdot \mathrm{Rt};, \tag{19}\end{equation*}$$

where *k* is the number of required servers in each scheme. Given the estimated Rt; limits $(\approx 0.53$ for $\Pi_{1}, \Pi_{2}, \Pi_{4}, \Pi_{5}$ and $\approx 1.06$ for $\Pi_{3}$), the estimated Rp*** limits (see Table IV) can be easily computed as well. A simple comparison shows that our experimental results of Rp*** (as shown in Table II) for $m=2000$ are both lower bounded by and quite close to the estimated limits of Rp*** (as shown in Table IV). As per [(19)](https://ieeexplore.ieee.org/document/#deqn19), the Rp*** limits are linear in *k* and the Rt; limits, respectively. The client will benefit most from MSVC when *k* is small and the computing gap between client and servers is large.



**TABLE III** The Experimental Results of $\mathrm{Rp}^{\star}(m=2000)$

![Table III- The Experimental Results of $\mathrm{Rp}^{\star}(m=2000)$](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang.t3-131600b499-small.gif)

**TABLE IV** The Estimated Limits of $\mathrm{Rp}^{\star}(m\rightarrow\infty)$

![Table IV- The Estimated Limits of $\mathrm{Rp}^{\star}(m\rightarrow\infty)$](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang.t4-131600b499-small.gif)

![Fig. 3. - The value of $\mathrm{NC}=T_{\mathrm{k}}/(T_{\mathrm{n}}-T_{\mathrm{c}}^{*}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang3-131600b499-small.gif)

**Fig. 3.**

The value of $\mathrm{NC}=T_{\mathrm{k}}/(T_{\mathrm{n}}-T_{\mathrm{c}}^{*}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for degree-2 polynomial evaluations ($d=2, t=1,2,3$ and $m=200,400, \ldots$, 2000 in all schemes)

**Break-even points.** Only two of the schemes ($\Pi_{1}$ and $\Pi_{4}$) have a non-empty KeyGen algorithm. Fig. 3 plots the experimental results of Nt; for these schemes and for all settings of $(t, m)\in\{1,2,3\}\times\{200,400, 2000\}$. It shows that $\mathrm{Nt};\leq 7$ when *m* is large enough. That is, if Tk is accounted, then in terms of time cost the client will benefit from evaluating *F* at least 7 times. The actual values of Nm depend on our choices of the $\mathrm{Rp}_{0}^{\star}$, an upper bound of the Rp*** in [(16)](https://ieeexplore.ieee.org/document/#deqn16). If we choose $\mathrm{Rp}_{0}^{\star}=6$ in both schemes, then for $t=1,2,3$ we will respectively have $\mathrm{Nm}\leq 5,8,20$ in these schemes. That is, in terms of monetary cost the client will benefit from evaluating *F* at least 5, 8, and 20 times, respectively.

### F. Experimental Results: Polynomials of Higher Degree

We also consider higher-degree polynomials to have a more complete overview of performance. In these experiments, we choose $d=4,8,16,32,64$, choose $t=1$ and choose *q* to be either the 129-bit prime or the 253-bit prime from Section V-D. Given $(q, d)$, the scale of a problem instance $(F, \mathbf{x})\in \mathcal{P}(q, m, d)\times\Gamma_{q}^{m}$ can be measured with *m*. However, for our choice of *d*, a small *m* may result in a huge $n=\begin{pmatrix} m+d\\d\end{pmatrix}$, the number of coefficients in an *m*-variate polynomial of degree *d*. For example, for $(m, d)=(32,10)$, we have $n=1471442973$, which is too large for a real application. Instead of using *m*, we will use the number of coefficients in *F* to measure the scale of $(F, \mathbf{x})$. We try to experiment with homogeneous polynomials that have 1*06* coefficients. In particular, we choose (*d, m*) = (4, 69), (8, 18), (16, 10), (32, 7), (64, 6) such that it is possible to have a polynomial with 1*06* coefficients. For each setting of (*d, m*), we run the KeyGen, ProbGen and Verify on VM*c* and run the Compute on VM*s*. We also execute the native computation of ${F}(\mathbf{x})$ on VM*c*. We repeat each execution 5 times and record the average CPU time such that the standard deviations are within 5% of the means.

![Fig. 4. - The value of $\mathrm{RC}=(T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*})/T_{\mathrm{n}}$ in higher-degree polynomial evaluations ($d=4,8,16,32,64, t=1$ and $n=10^{6}$ in all schemes)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang4-131600b499-small.gif)

**Fig. 4.**

The value of $\mathrm{RC}=(T_{\mathrm{p}}+T_{\mathrm{v}}+T_{\mathrm{c}}^{*})/T_{\mathrm{n}}$ in higher-degree polynomial evaluations ($d=4,8,16,32,64, t=1$ and $n=10^{6}$ in all schemes)

![Fig. 5. - The value of $\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for higher-degree polynomial evaluations ($d=4,8,16,32,64, t=1$ and $n=10^{6}$ in all schemes)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang5-131600b499-small.gif)

**Fig. 5.**

The value of $\mathrm{Rp}^{\star}=T_{\mathrm{c}}/(T_{\mathrm{n}}-T_{\mathrm{p}}-T_{\mathrm{v}})$ for higher-degree polynomial evaluations ($d=4,8,16,32,64, t=1$ and $n=10^{6}$ in all schemes)

**Time cost.** Fig. 4 plots the experimental results of Rt; for the five schemes and for all settings of $(d, m)$. For $\Pi_{1}, \Pi_{2}, \Pi_{4}$ and $\Pi_{5}$, it shows that $\mathrm{Rt};\lt 0.7$. Thus, in terms of time cost, the client may benefit from using these schemes when the polynomial has $\geq 10^{6}$ coefficients. For $\Pi_{3}$, it shows that Rt > 1. Comparing with the degree-2 setting, these schemes have quite similar performances for every *d*$\in\{4,8,16,32,64\}$.

**Monetary cost.** Fig. 5 plots the experimental results of Rp*** for the five schemes and for all settings of (*d, m*). It shows that the Rp*** value of every scheme is increasing as *d* is increasing. In particular, these values are lower bounded by and quite close to the estimated limits of Rp*** in Table V.

SECTION VI.

## Applications

### A. Curve Fitting with Private Data Points

Given a set of data points $\{(x_{i}, y_{i})\}_{i=1}^{m}$ that result from an experiment or a real-life scenario, we may assume there is a function $y=f(x)$ that passes through the data points and perfectly represents the quantity of interest at all non-data points. Curve fitting [5] is the process of constructing a curve or function that has the best fit to the data points. When the data points exhibit a significant degree of error, the strategy is to derive a single curve that represents the general trend of the data and may be realized with *least squares regression* (LSR) [34]. When the data points are very precise, the strategy is to fit a curve or a series of curves that pass directly through each of the points and may be realized with *interpolation* [34].

**TABLE V** The Estimated Limits of $\mathrm{Rp}^{\star}(m\rightarrow\infty)$

![Table V- The Estimated Limits of $\mathrm{Rp}^{\star}(m\rightarrow\infty)$](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang.t5-131600b499-small.gif)

In LSR, to fit a degree-*d* polynomial $y=\sum_{j=0}^{d}a_{j}x^{j}$, the idea is to minimize $S=\sum_{i=1}^{m}\left(y_{i}-\sum_{j=0}^{d}a_{j}x_{i}^{j}\right)^{2}$, the sum of the squares of the residuals between the measured values $\{y_{i}\}_{i=1}^{m}$ and the values calculated with the model. The best fit is obtained by setting $\frac{\partial S}{\partial a_{i}}=0$ for $0\leq i\leq d$ and then solving a linear equation system in $\{a_{i}\}_{i=0}^{d}$. In particular, the solution of each *ai* may be represented as a rational function of the data points $\{(x_{i},y_{i})\}_{i=1}^{m}$. For example, for $d=1$, both the numerator and the denominator of the rational function are polynomials of degree $\leq 3$ in the *2m* variables $\{(x_{i},y_{i})\}_{i=1}^{m}$. In general, fitting with a degree- *d* polynomial requires one to evaluate polynomials whose degrees are determined by *d*. In particular, small values of *d* are usually preferred and approached first [58]. Therefore, low-degree polynomial evaluations will be frequently used. The same situation occurs in multiple linear regression and interpolation.

In real-life scenarios, the data points may contain highly sensitive personal information such as the patients’ brain white matter microstructure [77] and inspiratory pressure [65]. Our schemes allow the client to outsource curve fitting with private data points to cloud, without leaking the personal information.

### B. PIR with Cheating Detection

PIR [37] is a cryptographic primitive that has important real applications [40]. It allows a client to retrieve an item *Fi* from a database $F=(F_{1}, F_{2}, \ldots, F_{n})$ and reveals no information about *i* to the database server(s). In a *t*-private *k*-server PIR [94], each server keeps a copy of *F* and answers to the client’s query, the client reconstructs *Fi* from all servers’ answers, and any *t* servers cannot learn *i*. The communication complexity of PIR is the total number of communicated bits for retrieving one bit from the database. The low-degree polynomial interpolation based *t*-private *k*-server PIR [37], [94] have nontrivial communication complexity of $O(n^{1/\lfloor(2k-1)/t\rfloor})$. Despite of efficient communication, PIR has not been widely deployed due to high computational complexity [14]. One can offload the PIR servers’ computations to cloud [66], which however may be untrusted and respond incorrectly. It is an interesting problem [13] to deal with dishonest PIR servers.

The *t*-private *t*-Byzantine robust *k*-server PIR schemes $((t, t, k)$-BRPIR) of [15], [73] allow the client to reconstruct correctly, even if *t* servers provide wrong answers. By using error-correcting techniques, they provide *information-theoretic* security and enable the *identification* of cheating servers. In particular, the techniques of [15], [73] may result in $(t, t, k)$-BRPIR with communication complexity $O(n^{1/(\lfloor(2k-1)/t\rfloor-4)})$, which is the best to date for general *t*. There are also schemes [44], [55] that are mostly suitable for retrieving $O(n^{1/2})$ bits per query and have communication complexity $O(n^{1/2})$.

Identification of cheating servers may be overly strong in many scenarios such as private media browsing [62], where a database of movies is stored on several non-communicating servers and the client hides its media diet by using PIR to retrieve a movie. In this scenario, it may suffice for the client to detect the existence of cheating and then refuse to pay. If we consider this relaxed security, then it may be possible to obtain meaningful efficiency improvements.

In the literature, *t*-private *k*-server PIR schemes [94] with best communication complexity for general *t* are based on low-degree multivariate polynomial evaluations. Given a prime *q*, the database *F* may be regarded as a vector over $\mathbb{F}_{q}$. Let $m,d\gt 0$ be integers such that $\binom{m}{d}\geq n$. With an injection $E:[n]\rightarrow\{0,1\}^{m}$ that maps $[n]$ to 0-1 vectors of weight $d,F$ may be encoded as $F(x_{1},\ldots,x_{m})=\sum_{j=1}^{n}F_{j}\prod_{s:E(j)_{s}=1}x_{s}$, a polynomial in $\mathcal{P}(q,m,d)$ such that $F(E(i))=F_{i}$ for all $i\in[n]$. Then the work of retrieving *Fi* can be reduced to the work of computing $F(\mathbf{x})$ at $\mathbf{x}=E(i)$. The MSVC schemes in Section III and IV directly translate to *t*-private *k*-server PIR schemes that can detect the cheating of *t* servers. In particular, $\Pi_{3}$ gives a scheme with communication complexity $O(n^{1/\lfloor(k-1)/t\rfloor})$. By using the partial derivative-based technique of [94], this communication complexity can be further reduced to $O(n^{1/\lfloor(2k-1)/t\rfloor})$, which is asymptotically better than the schemes of [15], [73]. If we consider computational verifiability, then $\Pi_{4}$ and $\Pi_{5}$ would yield PIR schemes that allow public detection of cheating.

#### Theorem 6.

*Let $\mu\left(k,t\right)=\max\left\{\lfloor\frac{k-1}{t+1}\rfloor,\lfloor\frac{k-1}{t}\rfloor-1\right\}$. There is a t-private k-server PIR scheme that allows public detection of cheating by t servers with communication complexity $O\left(n^{\frac{1}{\mu\left(k,t\right)}}\right)$. (see Appendix H)*

By using the partial derivative-based technique of [94], the number of servers in Theorem 6 may be halved.

#### Theorem 7.

*Let $v\left(k, t\right)=\max\left\{\lfloor\frac{2k-1}{t+1}\rfloor, \lfloor\frac{2k-1}{t}\rfloor-1\right\}$. There is a t-private k-server PIR that allows public detection of cheating by t servers with communication complexity $o\left(n\frac{1}{\nu\left(kt\right)}\right)$.*

**Comparison with BRPIR.** As $v(k, t)\gt\lfloor(2k-1)/t\rfloor-4$, the PIR in Theorem 7 is more efficient than the $(t, t, k)$-BRPIR of [15], [73], in terms of communication. As the price, the security is relaxed to cheating detection.

**Comparison with PIR for honest servers.** Comparing with PIR for honest servers, PIR with cheating detection needs additional cost. We implement the *t*-private *k*-server PIR (wyPIR) of [94] and the PIR from Theorem 6 (a $\Pi_{4}$-based PIR4 and a $\Pi_{5}$-based PIR5), using the security parameters, cyclic groups, libraries and platforms from Section V-D. We choose $t=1,(d, m)\in\{(2, 1415), (3, 183), (4, 72), (5, 44), (6, 33), (7, 28)\}$, and experiment with a database of $n=10^{6}$ blocks of 257 bits. Fig. 6 plots the maximum and total running time of servers, the total running time of the client, and the communication complexity in each scheme. It shows that:



- The maximum running time of servers in all all schemes is roughly equal to each other;
- The total running times of servers in PIR4 and PIR5 are within 2 and 1.5 times that of wyPIR, respectively;
- The client’s running time in PIR4 and PIR5 are within 15 and 6 times that of wyPIR, respectively; and
- The communication complexities of PIR4 and PIR5 are within 2 and 1.5 times that of wyPIR, respectively.

![Fig. 6. - Cost for retrieving a 257-bit block from a database 106 blocks (wyPIR is the baseline protocol [94]; PIR4 and PIR5 are based on $\Pi_{4}$ and $\Pi_{5}$ respectively; all without using the partial derive technique)](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9833550/9833558/9833792/zhang6-131600b499-small.gif)

**Fig. 6.**

Cost for retrieving a 257-bit block from a database *106* blocks (wyPIR is the baseline protocol [94]; PIR4 and PIR5 are based on $\Pi_{4}$ and $\Pi_{5}$ respectively; all without using the partial derive technique)

**On the number of servers.** In order to use degree-d polynomial evaluations to do *t*-private PIR with the same communication complexity $O(n^{1/d})$, PIR4 and PIR5 use $d(t+1)+1$ and $(d+1)t+1$ servers, respectively. They are more than the $dt+1$ servers used by wyPIR. Furthermore, PIR4 uses less servers than PIR5 if and only if $d\lt t$.

## Conclusions

In this paper, we define a new MSVC model and construct five MSVC schemes for outsourcing low-degree polynomials over a finite field, of which three are (information-theoretically) privately verifiable and two are publicly verifiable. All schemes are publicly delegatable, information-theoretically private, and outsourceable, and have highly efficient server computations. Our schemes yield multi-server PIR schemes that can detect the existence of cheating.

## Appendix AProof For Theorem 1

**Proof for input privacy.** According to Definition 4, we need to show that for any set $T\subseteq[k]$ of cardinality $\leq t$, any $F\in\mathcal{P}(q,m,d)$, and any $\mathbf{x}^{0},\mathbf{x}^{1}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{1}}(T,F,\mathbf{x}^{0})$ and $\sigma_{\Pi_{1}}(T,F,\mathbf{x}^{1})$ are identically distributed. It suffices to take $T=[t]$ and show that for any $\mathbf{x}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{1}}(T,F,\mathbf{x})$ is uniformly distributed over $\mathbb{F}_{q}^{mt}$. The specifications of $\Pi_{1}$ show that ProbGen $(\mathrm{pk}_{F},\mathbf{x})$ will output $\sigma_{i}=c(i)$ for every $i\in[k]$. In particular, $\sigma_{T}=(\sigma_{i})_{i\in T}$ will satisfy the equation system

$$\begin{align*}&\underbrace{\begin{pmatrix}1-\frac{1}{\alpha^{t}} & 1-\frac{1}{\alpha^{t-1}} & \cdots & 1-\frac{1}{\alpha} \\ 2-\frac{2^{\alpha^{2}+1}}{\alpha^{t}} & 2^{2}-\frac{2^{t+1}}{\alpha^{t-1}} & \cdots & 2^{t}-\frac{2^{t+1}}{\alpha} \\ \vdots & \vdots & \cdots & \vdots \\ t-\frac{t^{t+1}}{\alpha^{t}} & t^{2}-\frac{t^{t+1}}{\alpha^{t-1}} & \cdots & t^{t}-\frac{t^{t+1}}{\alpha}\end{pmatrix}}_{G}\begin{pmatrix}\mathbf{r}_{1}\\\mathbf{r}_{2}\\\vdots\\\mathbf{r}_{t}\end{pmatrix}= \\&\begin{pmatrix}\sigma_{1}-\mathbf{x}-\frac{1}{\alpha^{t+1}}(\mathbf{a}-\mathbf{x})\\\sigma_{2}-\mathbf{x}-\frac{2^{t+1}}{\alpha^{t+1}}(\mathbf{a}-\mathbf{x})\\\vdots\\\sigma_{t}-\mathbf{x}-\frac{t^{t+1}}{\alpha^{t+1}}(\mathbf{a}-\mathbf{x})\end{pmatrix} \tag{20}\end{align*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

Note that *G* is non-singular as $\alpha\notin[t]$. For any choice of $\sigma_{T}$, there is a unique set of random vectors $\mathbf{r}_{1},\mathbf{r}_{2},\ldots,\mathbf{r}_{t}$ such that [(20)](https://ieeexplore.ieee.org/document/#deqn20) is satisfied. Hence, $\sigma_{T}$ is uniformly distributed over $\mathbb{F}_{q}^{mt}$.

**Proof for security.** By Definition 2, it suffices to show that for any set $T\subseteq[k]$ of cardinality *t*, any $F\in\mathcal{P}(q,m,d)$, any adversary $\mathcal{A},{Pr}[{Exp}_{\mathcal{A},\Pi_{1}}^{{PriV}}(T,F,\lambda)=1]\leq\epsilon$. W.l.o.g., we take $T=[t]$ and consider the Experiment 1:



- *The challenger mimics ${KeyGen}(\lambda,F)$ as follows: choose $\ell_{0},\ell_{1}\leftarrow\mathbb{F}_{q}^{m}$, let $\ell(u)=\ell_{0}+\ell_{1}u$ and $\rho_{i}=F$ for every $i\in[k]$, compute $f(u)=F(\ell(u))$, set $\mathrm{pk}_{F}=\ell(u)$ and ${vk}_{F}=(\ell(u),f(u))$. It then invokes the adversary $\mathcal{A}$ with $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$.*
- *Given $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T}),\mathcal{A}$ chooses an input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ and gives it to the challenger.*
- *The challenger mimics ProbGen $(\mathrm{pk}_{F},\mathbf{x})$ as follows: choose $a\leftarrow\mathbb{F}_{q}^{\*},\alpha\leftarrow\mathbb{F}_{q}^{\*}\backslash[k],\mathbf{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow\mathbb{F}_{q}^{m}$, compute $\mathbf{a}=\ell\left(a\right),\mathbf{r}_{t+1}=\alpha^{-t-1}\left(\mathbf{a}-\left(\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}\alpha^{s}\right)\right)$, define $c(u)=\mathbf{x}+\sum_{s=1}^{t+1}\mathbf{r}_{s}u^{s}$, set $\mathrm{vk}_{\mathbf{x}}=(a,\alpha)$ and $\sigma_{i}=c(i)$ for all $i\in[k]$. It then gives $\sigma_{T}$ to $\mathcal{A}$.*
- *$\mathcal{A}$ chooses t partial results $\hat{\pi}_{T}=(\hat{\pi}_{1},\ldots,\hat{\pi}_{t})\in\mathbb{F}_{q}^{t}$ and gives them to the challenger.*
- *For every $i\in[k]\backslash T$, the challenger computes $\hat{\pi}_{i}\leftarrow$ Compute $(i,\rho_{i},\sigma_{i})$.*
- *The challenger mimics Verify $(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\hat{\pi}_{i}\}_{i=1}^{k})$ as follows: interpolate a polynomial $\hat{\phi}(u)$ of degree $\lt k$ such that $\hat{\phi}(i)=\hat{\pi}_{i}$ for all $i\in[k]$. If $\hat{\phi}(\alpha)=f(a)$, set $\hat{y}=\hat{\phi}(0)$; otherwise, set $\hat{y}=\bot$.*
- *If $\hat{y}\notin\{\bot,F(\mathbf{x})\}$, then outputs 1; otherwise, outputs 0.*



For every $i\in[k]$, let $\pi_{i}$ be the output of correctly executing Compute $(i,\rho_{i},\sigma_{i})$. Then $\pi_{i}=\hat{\pi}_{i}$ for every $i\in[k]\backslash T$; and $\phi(u)=F(c(u))$ is the polynomial of degree $\lt k$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. Note that $\hat{y}\neq\bot$ if and only if $\hat{\phi}(\alpha)=f(a)$. As $ f(a)=\phi(\alpha),\hat{y}\neq\bot$ if and only if

$$\begin{equation*}\hat{\phi}(\alpha)=\phi(\alpha). \tag{21}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

On the other hand, $\phi(0)=F(\mathbf{x})$. When [(21)](https://ieeexplore.ieee.org/document/#deqn21) is true, we have that $\hat{y}=\hat{\phi}(0)$ and thus $\hat{y}\neq F(\mathbf{x})$ if and only if

$$\begin{equation*}\hat{\phi}(0)\neq\phi(0). \tag{22}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

[Equation (22)](https://ieeexplore.ieee.org/document/#deqn22) requires that $\triangle(u)=\hat{\phi}(u)-\phi(u)$ is a nonzero polynomial and [(21)](https://ieeexplore.ieee.org/document/#deqn21) requires that the random field element $\alpha$ is a root of the degree $\lt k$ polynomial $\triangle(u)$. As $\triangle(u)$ has at most $k-1$ roots in $\Gamma_{q}^{*}\backslash[k]$ and $\alpha\in\Gamma_{q}^{*}\backslash[k]$ is randomly chosen and completely hidden from $\mathcal{A}$ (which can be seen from [(20)](https://ieeexplore.ieee.org/document/#deqn20)), we have that

$$\begin{align*}&{Pr}[{Exp}_{\mathcal{A},\Pi_{1}}^{{PriV}_{1}}(T,F,\lambda)=1]\\=&{Pr}[(\hat{y} \neq \perp) \wedge(\hat{y} \neq F(\mathbf{x}))]\\=&{Pr}[(\hat{\phi}(\alpha)=\phi(\alpha)) \wedge(\hat{\phi}(0) \neq \phi(0))]\\\leq&{Pr}[(\hat{\phi}(\alpha)=\phi(\alpha)) \mid(\hat{\phi}(0) \neq \phi(0))]\\\leq&(k-1)/(q-1-k).\end{align*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

Hence, $\Pi_{1}$ is $(t, \epsilon)$-secure with $\epsilon=\frac{d(t+1)}{q-2-d(t+1)}$.

## Appendix BProof For Theorem 2

**Proof for input privacy.** By Definition 4, we need to show that for any set $T\subseteq[k]$ of cardinality *t*, any $F\in\mathcal{P}(q,m,d)$, and any $\mathbf{x}^{0},\mathbf{x}^{1}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{2}}(T,F,\mathbf{x}^{0})$ and $\sigma_{\Pi_{2}}(T,F,\mathbf{x}^{1})$ are identically distributed. It suffices to set $T=[t]$ and show for any $\mathbf{x}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{2}}(T,F,\mathbf{x})$ is uniformly distributed over the set $\mathbb{F}_{q}^{(m+1)t}$. However, this is obvious because the input shares are all computed with Shamir’s threshold scheme [87].

**Proof for security.** By Definition 2, it suffices to show that for any $T\subseteq[k]$ of cardinality *t*, any $F\in\mathcal{P}(q,m,d)$, any adversary $\mathcal{A},{Pr}[{Exp}_{\mathcal{A},\Pi_{2}}^{{Pr}_{2}}(T,F,\lambda)=1]\leq\epsilon$. W.l.o.g., we take $T=[t]$ and consider Experiment 1:



- *The challenger mimics ${KeyGen}(\lambda,F)$ as follows: let $\rho_{i}= F$ for all $i\in[k]$, and set $\mathrm{pk}_{F}=$ and $\mathrm{vk}_{F}=$. It then invokes $\mathcal{A}$ with $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$.*
- *Given $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T}),\mathcal{A}$ chooses an input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ and gives it to the challenger.*
- *The challenger mimics ProbGen $(\mathrm{pk}_{F},\mathbf{x})$ as follows: choose $\alpha\leftarrow\mathbb{F}_{q}^{\*},\mathbf{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow\mathbb{F}_{q}^{m},\gamma_{1},\ldots,\gamma_{t}\leftarrow\mathbb{F}_{q}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}u^{s}$ and $b(u)=\alpha+\sum_{s=1}^{t}\gamma_{s}u^{s}$, compute $\sigma_{i}=(c(i),b(i))$ for all $i\in[k]$, and set $\mathrm{vk}_{\mathbf{x}}=\alpha$. It then gives $\sigma_{T}$ to $\mathcal{A}$.*
- *A chooses t partial results $\{(\hat{v}_{i},\hat{w}_{i})\}_{i\in T}$ and gives them to the challenger:*
- *For every $i\in[k]\backslash T$, the challenger computes $(\hat{v}_{i},\hat{w}_{i})\leftarrow$ Compute $(i,\rho_{i},\sigma_{i})$.*
- *The challenger interpolates a polynomial $\hat{\phi}(u)$ of degree $\leq$ dt such that $\hat{\phi}(i)=\hat{v}_{i}$ for all $i\in[k]$; and a degree $\leq(d+1)$ t polynomial $\hat{\psi}(u)$ such that $\hat{\psi}(i)=\hat{w}_{i}$ for all $i\in[k]$. If $\hat{\psi}(0)=\alpha\hat{\phi}(0)$, then it sets $\hat{y}=\hat{\phi}(0)$; otherwise, it sets $\hat{y}=$.*
- *If $\hat{y}\notin\{,F(\mathbf{x})\}$, then outputs 1; otherwise, outputs 0.*



It’s easy to see that $\hat{y}\neq$ if and only if $\hat{\psi}(0)=\alpha\hat{\phi}(0)$. For $\phi(u)=F(c(u))$ and $\psi(u)=\phi(u)b(u)$, we always have that $\psi(0)=\alpha\phi(0)$. Thus, the event $\hat{y}\neq$ occurs if and only if

$$\begin{equation*}\hat{\psi}(0)-\psi(0)=\alpha(\hat{\phi}(0)-\phi(0)). \tag{23}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

On the other hand, $\phi(0)=F(\mathbf{x})$. When [(23)](https://ieeexplore.ieee.org/document/#deqn23) is true, we will have that $\hat{y}=\hat{\phi}(0)$ and thus $\hat{y}\neq F(\mathbf{x})$ if and only if $\hat{\phi}(0)\neq\phi(0)$. Note that $\alpha\in\mathbb{F}_{q}^{*}$ is randomly chosen and completely hidden from $\mathcal{A}$ (i.e., $\mathcal{S}_{T}$) due to the security of Shamir’s threshold scheme. Thus, we have that

$$\begin{align*}&{Pr}\left[{Exp}_{\mathcal{A},\Pi_{2}}^{\mathrm{PriV}_{2}}\left(T,F,\lambda\right)=1\right]\\=&{Pr}\left[\left(\hat{y} \neq \perp\right) \wedge\left(\hat{y} \neq F\left(\mathbf{x}\right)\right)\right]\\=&{Pr}\left[\alpha=\frac{\hat{\psi}\left(0\right)-\psi\left(0\right)}{\hat{\phi}\left(0\right)-\phi\left(0\right)}\wedge\left(\hat{\phi}\left(0\right)\neq\phi\left(0\right)\right)\right]\\\leq&{Pr}\left[\alpha=\frac{\hat{\psi}\left(0\right)-\psi\left(0\right)}{\hat{\phi}\left(0\right)-\phi\left(0\right)}\mid\left(\hat{\phi}\left(0\right)\neq\phi\left(0\right)\right)\right]\\\leq&1/\left(q-1\right).\end{align*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

Hence, $\Pi_{2}$ is $(t,\epsilon)$-secure with $\epsilon=1/(q-1)$.

## Appendix CProof For Theorem 3

**Proof for input privacy.** By Definition 4, we need to show that for any set $T\subseteq[k]$ of cardinality *t*, any $F\in\mathcal{P}(q,m,d)$, and any $\mathbf{x}^{0},\mathbf{x}^{1}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{3}}(T,F,\mathbf{x}^{0})$ and $\sigma_{\Pi_{3}}(T,F,\mathbf{x}^{1})$ are identically distributed. It suffices to take $T=[t]$ and show that for any $\mathbf{x}\in\mathbb{F}_{q}^{m},\sigma_{\Pi_{3}}(T,F,\mathbf{x})$ is uniformly distributed over $\mathbb{F}_{q^{2}}^{mt}$. In an execution of $\Pi_{3},{ProbGen}(\mathrm{pk}_{F},\mathbf{x})$ will output an input share $\sigma_{i}=c(i)$ for all $i\in[k]$. In particular, $\sigma_{T}= (\sigma_{i})_{i\in T}$ will satisfy the following equation system

$$\begin{equation*}\underbrace{\begin{pmatrix}1-\alpha & 1-\alpha^{2} & \cdots & 1-\alpha^{t} \\2-\alpha & 2^{2}-\alpha^{2} & \cdots & 2^{t}-\alpha^{t} \\\vdots & \vdots & \cdots & \vdots \\t-\alpha & t^{2}-\alpha^{2} & \cdots & t^{t}-\alpha^{t}\end{pmatrix}}_{G}\begin{pmatrix}\mathbf{r}_{1}\\\mathbf{r}_{2}\\\vdots\\\mathbf{r}_{t}\end{pmatrix}=\begin{pmatrix}\sigma_{1}-\mathbf{x}\\\sigma_{2}-\mathbf{x}\\\vdots\\\sigma_{t}-\mathbf{x}\end{pmatrix}.\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)

Note that the coefficient matrix *G* is non-singular because $\alpha\notin [t]$. For any value of $\sigma_{T}\in\mathbb{F}_{q^{2}}^{mt}$, there is a unique set of vectors $\mathbf{r}_{1},\ldots,\mathbf{r}_{t}$ such that the above equation system is satisfied. Hence, $\sigma_{T}$ is uniformly distributed over $\mathbb{F}_{q^{2}}^{mt}$

**Proof for security.** By Definition 2, it suffices to show that for any $T\subseteq[k]$ of cardinality *t*, any $F\in\mathcal{P}(q,m,d)$, any adversary $\mathcal{A},{Pr}[{Exp}_{\mathcal{A},\Pi_{3}}^{{PriV}^{2}}(T,F,\lambda)=1]\leq\epsilon$. W.l.o.g., we take $T=[t]$ and consider Experiment 1:



- *The challenger mimics ${KeyGen}(\lambda,F)$ as follows: let $\rho_{i}= F$ for all $i\in[k],\mathrm{pk}_{F}=\bot$, and $\mathrm{vk}_{F}=\bot$. It then invokes $\mathcal{A}$ with $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$.*
- *Given $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T}),\mathcal{A}$ chooses an input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ and gives it to the challenger.*
- *The challenger mimics ProbGen $(\mathrm{pk}_{\mathrm{F}},\mathbf{x})$ as follows: choose $\alpha\leftarrow\mathrm{F}_{q^{2}}^{\*}\backslash[k],\mathbf{r}_{1},\ldots,\mathrm{r}_{\mathrm{t}}\leftarrow\mathrm{F}_{q^{2}}^{m}$, let $c(u)= \mathbf{x}+\sum_{s-1}^{t}\mathbf{r}_{\bar{n}}(u^{n}-\alpha^{a})$, set $\mathrm{vk}_{\mathbf{x}}=\alpha$ and $\sigma_{i}=c(i)$ for all $i\in[k]$. It then gives $\sigma_{T}$ to $\mathcal{A}$.*
- *A chooses \*t* partial results $\{\hat{\pi}_{i}\}_{1\in T}$ and gives them to the challenger.*
- *For every $i\in[k]\backslash T$, the challenger computes $\hat{\pi}_{i}\leftarrow$ Compute $(i,\rho_{i},\sigma_{i})$.*
- *The challenger interpolates a polynomial $\hat{\phi}(u)$ of degree $\leq dt$ such that $\hat{\phi}(\dot{}{i})=\hat{\pi}_{1}$ for all $i\in[k]$. If $\hat{\phi}(\alpha)\in\mathbb{F}_{q}$ it sets $\hat{y}=\hat{\phi}(\alpha)$; otherwise, it sets $\hat{y}=\bot$*
- *If $\hat{y}\notin\{,F(\mathbf{x})\}$, then outputs 1; otherwise, outputs 0.*



The event ${Exp}{PriV}_{\mathcal{A},\Pi_{a}}(T,F,\lambda)=1$ occurs if and only if $\hat{\phi}(\alpha)\in\mathrm{F}_{q}\backslash\{F(\mathbf{x})\}$, i.e., if and only if $\alpha$ is a root of at least one of the $q-1$ polynomials in $\left\{\hat{\phi}\left(u\right)-\delta:\delta\in\mathrm{F}_{q}\backslash\left\{F\left(\mathbf{x}\right)\right\}\right\}$. Note that $\mathcal{A}$ knows nothing about $\alpha\in\mathbb{F}_{\mathrm{q}^{2}}^{*}\backslash[k]$, which was randomly chosen. We must have that ${Pr}[\hat{\phi}(\alpha)\in\mathbb{F}_{q}\backslash\{F(\mathbf{x})\}]\leq(q-1)dt/(q^{2}-1-k)$. Hence, $\Pi_{3}$ is $(t,\epsilon)$-secure for $\epsilon=(q-1)dt/(q^{2}-2-dt)$.

## Appendix DCryptographic Assumptions

#### Definition 5. (DLog Assumption): 

*Let G be a cyclic group of order $q\gt2^{\lambda}$. For a generator $g\in\mathbb{G}$ and $\alpha\leftarrow\mathbb{Z}_{q}$ we define the advantage of an adversary A in solving the DLog problem as ${Adv}_{\mathcal{A}}^{\mathrm{Dt}}(\lambda)={Pr}[\mathcal{A}(g,g^{\alpha})=\alpha]$. We say that the DLog assumption holds in $\mathbb{G}$ if for any PPT adversary $A.\ {Adv}_{\mathcal{A}}^{\mathrm{DL}}(\lambda)\leq negl(\lambda)$.*

#### Definition 6. (D-DHI Assumption*[30]*): 

*Let $\mathbb{G}$ be a cyclic group of order $q\gt2^{\lambda}$. For a generator $g\in\mathbb{G}$ and $\alpha\leftarrow\mathbb{Z}_{q}$ we define the advantage of an adversary $\mathcal{A}$ in solving the D-DHI problem as $\mathbf{A}d\mathbf{v}_{\mathcal{A}}^{\mathrm{DHI}}(\lambda)={Pr}[\mathcal{A}(g,g^{\alpha},\ldots,g^{\alpha^{D}})=g^{1/\alpha}]$ and we say that the D-DHI assumption holds in $\mathbb{G}$ if for any PPT adversary $\mathcal{A}$ and for $D=$ poly $(\lambda),{Adv}_{\mathcal{A}}^{\mathrm{DHI}}(\lambda)\leq\mathrm{negl}(\lambda)$.*

## Appendix EProof For Theorem 4

By Definition 3, it suffices to show that for any $T\subseteq[k]$ of cardinality $\leq t$, any $F\in\mathcal{P}(q,m,d)$, any PPT adversary $\mathcal{A},\epsilon:={Pr}[{Exp}_{\mathcal{A}^{{MabV}}}\boldsymbol{\Pi}_{4}(T,F,\lambda)=1]$ must be negligible in $\lambda$. W.l.o.g., we take $T=[t]$ and construct a PPT algorithm $\mathcal{B}$ that uses $\mathcal{A}$ to solve the $(k-1)$-DHI problem in $\mathbb{G}$:



- *Given $I=(g,g^{\alpha},\ldots,g^{\alpha^{k-1}}),\mathcal{B}$ mimics ${Key}{Gen}(\lambda,F)$ as follows: choose $\ell_{0},\ell_{1}\leftarrow F_{q}^{m}$, let $\ell(u)=\ell_{0}+\ell_{1}u$, compute $f(u)=F(\ell(u))$, set $\rho_{\mathrm{s}}=F$ for every $i\in[k], \mathrm{pk}_{F}=\ell(u)$ and $\mathrm{vk}_{F}=(\ell(u),f(u))$. It then invokes $\mathcal{A}$ with $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$.*
- *A chooses an input $\mathbf{x}\in\mathrm{F}_{q}^{\mathrm{m}}$ and gives it to \*B*.*
- **B\* mimics ProbGen $(\mathrm{pk}_{F},\mathbf{x})$ as follows: choose $a\leftarrow\mathbb{F}_{q}^{\*}, \sigma_{1},\sigma_{2},\ldots,\sigma_{t}\leftarrow\mathbb{F}_{q}^{rn}$, set $\mathrm{vk}_{\mathbf{x}}=(g,g^{a},\ldots,g^{a^{d}},g^{\alpha},\ldots,g^{\alpha^{k-1}})$. It then gives both $vk_{\mathbf{x}}$ and and $\sigma_{T}$ to \*A*.*
- *A chooses $\hat{\pi}_{T}=\{\hat{\pi}_{1}\}_{1\in T}\in\mathbb{F}_{q}^{t}$ and gives it to \*B*.*
- **B\* computes $\pi_{i}=F(\sigma_{i})$ for all $i\in[t]$ and interpolates a polynomial $\delta(u)=\sum_{1-0}^{k-1}\delta_{\mathrm{q}}u^{t}$ of degree $\lt k$ such that $\delta(i)=\hat{\pi}_{i}-\pi_{i}$ for all $i\in[t]$ and $\delta(i)=0$ for all $t\lt i\leq k$. Note that $\mathcal{B}$ is able to compute $g^{\delta(\alpha)}$ with \*I* and the coefficients of $\delta(u)$. If $\delta(0)\neq 0$ and $g^{\delta(\alpha)}=1, \mathcal{B}$ outputs* $$\begin{equation*}g^{1/\alpha}=\left(\prod_{i=1}^{k-1}g^{\delta_{i}\alpha^{i-1}}\right)^{-\frac{1}{\delta_{0}}}, \tag{24}\end{equation*}$$View Source![Right-click on figure for MathML and additional features.](https://ieeexplore.ieee.org/assets/img/icon.support.gif)



Note that what $\mathcal{A}$ sees in this procedure is identically distributed to what it should see in ${Exp}_{\mathcal{A},\Pi_{4}}^{\mathrm{PubV}^{2}}(T,F,\lambda)$. In particular, the vectors $\mathbf{r}_{1},\ldots,\mathbf{r}_{t}\in\mathbb{F}_{q}^{m}$ used to encode $\mathbf{x}$ are implicit and uniquely determined with [(20)](https://ieeexplore.ieee.org/document/#deqn20). Furthermore, the $\mathbf{r}_{t+1}$ could be computed as $\mathbf{r}_{t+1}=\alpha^{-t-1}\left(\mathbf{a}-\left(\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}\alpha^{s}\right)\right)$ Although $\mathcal{B}$ is unable to compute the partial result $\pi_{i}$ for every $t\lt i\leq k$, we have that $\delta(u)=\hat{\phi}(u)-\phi(u)$, where $\hat{\phi}(u)$ is interpolated from $(\hat{\pi}_{1},\ldots,\hat{\pi}_{t},\pi_{t+1},\ldots,\pi_{k})$ such that $\phi(i)=\hat{\pi}_{i}$ for all $i\in[t]$ and $\phi(i)=\pi_{i}$ for all $t\lt i\leq k$, and $\phi(u)$ is interpolated from $(\pi_{1},\ldots,\pi_{t},\pi_{t+1},\ldots,\pi_{k})$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. It is clear that $\mathcal{A}$ wins in the security experiment if and only if $\delta(0)\neq 0$ and $g^{\delta(\alpha)}=1$, where “$g(\alpha)=1$” signs that the partial results chosen by $\mathcal{A}$ will be accepted and “$\delta(0)\neq 0$” signs that Verify will output a value $\neq F(\mathbf{x})$. When $\mathcal{A}$ wins, [(24)](https://ieeexplore.ieee.org/document/#deqn24) will allow $\mathcal{B}$ to learn $g^{1/\alpha}$. Hence, we have that ${Pr}[\mathcal{B}(I)=g^{1/\alpha}]={Pr}[{Exp}_{\mathcal{A},\Pi_{4}}^{\mathrm{Pub}^{\mathrm{H}}}(T,F,\lambda)=1]$ $=\epsilon$. Under the $(k-1)$-DHI assumption in $\mathbb{G},\epsilon$ must be negligible in $\lambda$. Hence, $\Pi_{4}$ should be *t*-secure under the same assumption.

## Appendix FProof For Theorem 5

By Definition 3, it suffices to show that for any $T\subseteq[k]$ of cardinality $\leq t$, any $F\in\mathcal{P}(q,m,d)$, any PPT adversary $\mathcal{A}, \epsilon:={Pr}[{Exp}_{\mathcal{A}_{\mathcal{A},\Pi_{5}}}^{\mathrm{PubV}}(T,F,\lambda)=1]$ is negligible in $\lambda$. Without loss of generality, we take $T=[t]$ and construct a PPT algorithm $\mathcal{B}$ that uses $\mathcal{A}$ to solve the DLog problem in $\mathbb{G}$:



- *Given $I=(g,g^{\alpha}),\mathcal{B}$ mimics ${KeyGen}(\lambda,F)$ as follows: let $\rho_{i}=F$ for all $i\in[k],\mathrm{pk}_{F}=$ and $\mathrm{vk}_{F}=$. It then invokes $\mathcal{A}$ with $(F,\mathrm{pk}_{F},\mathrm{vk}_{F},\rho_{T})$.*
- *$\mathcal{A}$ chooses an input $\mathbf{x}\in\mathbb{F}_{q}^{m}$ and gives it to $\mathcal{B}$.*
- *$\mathcal{B}$ mimics ${ProbGen}(\mathrm{pk}_{F},\mathbf{x})$ as follows: choose $\mathrm{c}_{1},\ldots, \mathbf{c}_{t}\leftarrow\mathbb{F}_{q}^{m},b_{1},\ldots,b_{t}\leftarrow\mathbb{F}_{q}$, set $\mathrm{vk}_{\mathbf{x}}=g^{\alpha}$ and $\sigma_{i}= (\mathbf{c}_{i},b_{i})$ for every $i\in[t]$. It then gives $\mathrm{vk}_{\mathbf{x}}$ and $\sigma_{T}$ to $\mathcal{A}$.*
- *$\mathcal{A}$ chooses $\hat{\pi}_{i}=(\hat{v}_{i},\hat{w}_{i})\in\mathbb{F}_{q}^{2}$ for all $i\in[t]$ and gives $\hat{\pi}_{T}$ to $\mathcal{B}$.*
- *Let $v_{i}=F(\mathbf{c}_{i})$ and $w_{i}=v_{i}b_{i}$ for $i\in[t].\mathcal{B}$ interpolates a polynomial $\delta(u)$ of degree $\leq dt$ such that $\delta(i)=\hat{v}_{i}-v_{i}$ for all $i\in[t]$ and $\delta(i)=0$ for all $t\lt i\leq k$; and interpolates a polynomial $\Delta(u)$ of degree $\leq(d+1)t$ such that $\Delta(i)=\hat{w}_{i}-w_{i}$ for all $i\in[t]$ and $\Delta(i)=0$ for all $t\lt i\leq k$. If $g^{\Delta(0)}=g^{\alpha\delta(0)}$ and $\delta(0)\neq 0$, then $\mathcal{B}$ outputs $\alpha=\Delta(0)/\delta(0)$; otherwise, it outputs.* $\perp$.



Note that what $\mathcal{A}$ sees in the above procedure is identically distributed to what it should see in ${Exp}_{\mathcal{A},\Pi_{5}}^{\mathrm{PubV}_{5}}(T,F,\lambda)$. The vectors $r_{1},\ldots,\mathbf{r}_{t}\in\mathbb{F}_{q}^{m}$ and the numbers $\gamma_{1},\ldots,\gamma_{t}\in\mathbb{F}_{q}$ for encoding $\mathbf{x}$ are implicit but uniquely determined by $\sigma_{T},g^{\alpha}$ and $\mathbf{x}$. Although $\mathcal{B}$ is unable to compute the partial result $\pi_{i}= (v_{i},w_{i})$ for every $t\lt i\leq k$, it is true $\delta(u)=\hat{\phi}(u)-\phi(u)$, where $\hat{\phi}(u)$ is interpolated from $(\hat{v}_{1},\ldots,\hat{v}_{t},v_{t+1},\ldots,v_{k})$ such that $\hat{\phi}(i)=\hat{v}_{i}$ for all $i\in[t]$ and $\hat{\phi}(i)=v_{i}$ for all $t\lt i\leq k$, and $\phi(u)$ is interpolated from $(v_{1},\ldots,v_{k})$ such that $\phi(i)=v_{i}$ for all $i\in[k]$. Similarly, it is true that $\Delta(u)=\hat{\psi}(u)-\psi(u)$, where $\hat{\psi}(u)$ is interpolated from $(\hat{w}_{1},\ldots,\hat{w}_{t},w_{t+1},\ldots,w_{k})$ such that $\hat{\psi}(i)=\hat{w}_{i}$ for all $i\in[t]$ and $\hat{\psi}(i)=w_{i}$ for all $t\lt i\leq k$, and $\psi(u)$ is interpolated from $(w_{1},\ldots,w_{k})$ such that $\psi(i)=w_{i}$ for all $i\in[k]$. It is clear that $\mathcal{A}$ wins in the security experiment if and only if $\delta(0)\neq 0$ and $g^{\Delta(0)}=g^{\alpha\delta(0)}$, where “$g^{\Delta(0)}=g^{\alpha\delta(0)}$” signs that the partial results chosen by $\mathcal{A}$ will be accepted and “$\delta(0)\neq 0$” signs that Verify will output a value $\neq F(x)$. When $\mathcal{A}$ wins, $\mathcal{B}$ is to learn $\alpha=\Delta(0)/\delta(0)$. Hence, ${Pr}[\mathcal{B}(I)=\alpha]= {Pr}[{Exp}_{\mathcal{A},\Pi_{5}}^{\mathrm{PubV}^{2}}(T,F,\lambda)=1]=\epsilon$. Under the DLog assumption in $\mathbb{G},\epsilon$ must be negligible in $\lambda$. Hence, $\Pi_{5}$ is *t*-secure under the same assumption.

## Appendix GOutsourceability

#### Definition 7. (Outsourceability): 

*An MSVC scheme is said to be outsourceable if for any x and $\{\pi_{i}\}_{i=1}^{k}$, the time $T_{\mathrm{p}}+T_{\mathrm{v}}$ required by $\mathrm{ProbGen}(\mathrm{pk}_{F},x)$ and Verify $(\mathrm{vk}_{F},\mathrm{vk}_{x},\{\pi_{i}\}_{i=1}^{k})$ is $o(T_{\mathrm{n}})$, where Tn is the running time of the native computation $F(x)$.*

#### Theorem 8.

*If $kmt^{2}+k^{3}+(k+d)\lambda=o(nd)$, where $n= \binom{m+d}{d}$, then the five MSVC schemes are all outsourceable.*

#### Proof.

In $\Pi_{1},\Pi_{2}$ and $\Pi_{3}$, ProbGen and Verify require $O(kmt^{2})$ and $O(k^{3})$ field operations in $\mathbb{F}_{q}$ (or $\mathbb{F}_{q^{2}}$), respectively. The native computation requires $O(nd)$ field operations. Under the given condition, we have that $T_{\mathrm{p}}+T_{\mathrm{v}}=o(T_{\mathrm{n}})$ and thus these schemes are outsourceable. In $\Pi_{4}$ and $\Pi_{5}$, ProbGen requires $O(kmt^{2})$ field operations, and Verify requires both $O(k^{3})$ field operations and $O(k+d)$ exponentiations in $\mathbb{G}$, where the group operations are equivalent to around $O((k+$ d) $\lambda$) field operations. Under the given condition, we have that $T_{\mathrm{p}}+T_{\mathrm{v}}=o(T_{\mathrm{n}})$. Thus, $\Pi_{4}$ and $\Pi_{5}$ are outsourceable.

## Appendix HPIR With Cheating Detection

Let $d=\mu(k,t)$. By using $\Pi_{4}$ (when $k=d(t+1)+1$) or $\Pi_{5}$ (when $k=(d+1)t+1)$ to publicly and verifiably compute $F(E(i))$, the client has a PIR scheme that can detect the cheating of $\leq t$ servers. Its communication complexity is $O(n^{1/d})=O(n^{1/\mu(k,t)})$.







${KeyGen}(\lambda,F)$: Let $\rho_{i}=F$ for every $i\in[k]$. Output $\mathrm{pk}_{F}=, \mathrm{vk}_{F}= \bot $and $\{\rho_{i}\}_{i=1}^{k}$.

$ProbGen(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q}^{\*},\mathrm{r}_{1},\ldots,\mathrm{r}_{t}\leftarrow\mathbb{F}_{q}^{m},\gamma_{1},\ldots,\gamma_{t}\leftarrow \mathbb{F}_{q}, $let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}u^{s}$,$b(u)=\alpha+\sum_{s=1}^{t}\gamma_{s}u^{s}$, and $\sigma_{i}=(c(i),b(i)) $for all $i\in[k]$. Choose a cyclic group $\mathbb{G}=\langle g\rangle$ of order $q$. Output $\mathrm{vk}_{\mathbf{x}}=g^{\alpha}$ and $\{\sigma_{i}\}_{i=1}^{k}$.

$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as $F$,$\sigma_{i} $as $(c(i),b(i))$, compute $ v_{i}= F(c(i)),w_{i}=v_{i}b(i)$, output $\pi_{i}=(v_{i},w_{i})$.

$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$of degree $\leq dt$ such that $\phi(i)=v_{i}$ for all $i\in[k]$ and a polynomial $\psi(u)$ of degree $\leq(d+1)t$ such that $\psi(i)=w_{i}$ for all $i\in[k]$. If $g^{\psi(0)}=g^{\alpha\phi(0)}$, output $\phi(0)$; otherwise, output $\bot$.

${KeyGen}(\lambda,F)$: Let $\rho_{i}=F$ for all $i\in[k]$. Output $\mathrm{pk}_{F}=,$$\mathrm{vk}_{F}= \bot$ and $\{\rho_{i}\}_{i=1}^{k}$。

${ProbGen}(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q}^{\*}$,$\mathrm{r}_{1},\ldots,\mathbf{r}_{t}\leftarrow\mathbb{F}_{q}^{m},\gamma_{1},\ldots,\gamma_{t}\leftarrow \mathbb{F}_{q}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}u^{s}$ and $b(u)=\alpha+\sum_{s=1}^{t}\gamma_{s}u^{s}$, and compute $\sigma_{i}=(c(i),b(i))$ for all $i\in[k]$. Output $\mathrm{vk}_{\mathbf{x}}=\alpha$ and $\{\sigma_{i}\}_{i=1}^{k}$

$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as $F$, compute $v_{i}=F(c(i))$ and $w_{i}= v_{i}b(i)$, and output $\pi_{i}=(v_{i},w_{i})$.

$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq dt$ such that $\phi(i)=v_{i}$ for all $i\in[k]$ and a polynomial $\psi(u)$ of degree $\leq(d+1)t$ such that $\psi(i)=w_{i}$ for all i\in[k]. If $\psi(0)=\alpha\bar{\phi}(0)$, output $\phi(0)$; otherwise, output $\bot$.





### C. The Third Construction

In our third construction (Scheme 3), the client will work over $\Gamma_{q^{2}}$. Instead of choosing a random curve $c(u)$ that passes through $(0, \mathbf{x})$, it will choose a random curve

$$\begin{equation*}c(u)=\mathbf{x}+\mathbf{r}_{1}(u-\alpha)+\cdots+\mathbf{r}_{t}(u^{t}-\alpha^{t}) \tag{6}\end{equation*}$$

that passes through $(\alpha,\mathbf{x})$ at a random point $\alpha\leftarrow\mathbb{F}_{q^{2}}^{*}\backslash[k]$. Each server $\mathcal{S}_{i}$ will be given a curve point $(i,c(i))$ such that any *t* servers learn no information about $\mathbf{x}$. Given $k=dt+1$ servers, the client would be able to reconstruct the degree $\leq dt$ polynomial $\phi(u)=F(c(u))$ and learn $F(\mathbf{x})=\phi(\alpha)\in\mathbb{F}_{q}$.

### Scheme 3. The k-Server Scheme $\Pi_{3}(k=dt+1)$

*$KeyGen(\lambda,F)$: Let $\rho_{i}=F$ for all $i\in[k]$. Output $\mathrm{pk}_{F}=,\mathrm{vk}_{F}= \bot$ and $\{\rho_{i}\}_{i=1}^{k}$.*

$ProbGen(\mathrm{pk}_{F},\mathbf{x})$: Choose $\alpha\leftarrow\mathbb{F}_{q^{2}}^{\*}\backslash[k]$ and $\mathrm{r}_{1},\ldots,\mathrm{r}_{t}\leftarrow\mathbb{F}_{q^{2}}^{m}$, let $c(u)=\mathbf{x}+\sum_{s=1}^{t}\mathbf{r}_{s}(u^{s}-\alpha^{s})$, set $\sigma_{i}=c(i)$ for all $i\in[k]$.

Output ${vk}_{\mathbf{x}}=\alpha$ and $\{\sigma_{i}\}_{i=1}^{k}$.

$Compute(i,\rho_{i},\sigma_{i})$: Parse $\rho_{i}$ as \*F* and output $\pi_{i}=F(\sigma_{i})$.

$Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k})$: Interpolate a polynomial $\phi(u)$ of degree $\leq dt$ such that $\phi(i)=\pi_{i}$ for all $i\in[k]$. If $\phi(\alpha)\in\mathbb{F}_{q}$, output $\bar{\phi}(\alpha)$; otherwise, output $\bot$.

When the partial results $\{\pi_{i}\}_{i=1}^{k}$ are all correctly computed, we have $\pi_{i}=F(c(i))$ for all $i\in[k]$. Then the polynomial $\phi(u)$ interpolated in Verify is $F(c(u))$. The correctness of $\Pi_{3}$ follows from the fact that $\phi(\alpha)=F(c(\alpha))=F(\mathbf{x})\in\mathbb{F}_{q}$. The cheating servers may provide incorrect partial results, which then result in a polynomial $\hat{\phi}(u)\neq F(c(u))$ in Verify. The cheating will be detected if $\hat{\phi}(\alpha)\notin\mathbb{F}_{q}$. Therefore, $\hat{\phi}(u)$ will not allow the cheating servers to break the security unless $\hat{\phi}(\alpha)\in\mathbb{F}_{q}\backslash\{\phi(\alpha)\}$. However, without knowing $\alpha$, the last event will not occur except with very small probability.

#### Theorem 3.

*The scheme* $\Pi_{3}$ *is* *t*-*private and* $(t, \epsilon)$-*secure for* $\epsilon=\frac{(q-1)dt}{q^{2}-2-dt}$ (*see Appendix* *C*).









In all constructions, the basic idea of achieving *t*-privacy is secret-sharing the input \mathbf{x}\in\mathbb{F}_{q}^{m} among all servers using Shamir’s threshold scheme [87] for vectors. That is, the client draws a random degree- *t* curve

$$\begin{equation*}c(u)=\mathbf{x}+\mathrm{r}_{1}u+\cdots+\mathrm{r}_{t}u^{t} \tag{1}\end{equation*}$$

that resides in \mathbb{F}_{q}^{m} and passes through (0,\mathbf{x}), and then distributes *k* curve points to *k* servers. The *k* servers return the evaluations of *F* on *k* points, which will allow the client to interpolate a degree \leq dt polynomial

$$\begin{equation*}\phi(u)=F(c(u)) \tag{2}\end{equation*}$$

and then learn F(\mathbf{x})=F(c(0))=\phi(0). For the sake of polynomial interpolation, we always assume that k\lt q and each server \mathcal{S}_{i} is associated with a field element i\in\Gamma_{q}.

In our second construction (Scheme 2), the client will use $\begin{equation*}c(u)=\mathbf{x}+\mathrm{r}_{1}u+\cdots+\mathrm{r}_{t}u^{t} \tag{1}\end{equation*}$ 

to secret-share its input among the servers and use 

$\begin{equation*}\phi(u)=F(c(u)) \tag{2}\end{equation*}$ 

to reconstruct the output. To add verifiability, the client will additionally secret-share a random field element \alpha\leftarrow\Gamma_{q}^{*} among the *k* servers using a random polynomial

\begin{equation*}b(u)=\alpha+\gamma_{1}u+\cdots+\gamma_{t}u^{t} \tag{4}\end{equation*}

of degree \leq t. Each server returns not only F(c(i)) but also b(i)F(c(i)). Finally, the client interpolates two polynomials

$\begin{equation*}\phi(u)=F(c(u))\, \psi(u)=F(c(u))b(u) \tag{5}\end{equation*}$

of degree \leq dt and \leq(d+1)t, respectively. The verification is done by checking whether \psi(0)=\alpha\phi(0).

*Verify(\mathrm{vk}_{F},\mathrm{vk}_{\mathbf{x}},\{\pi_{i}\}_{i=1}^{k}): Interpolate a polynomial \phi(u) of degree \leq dt such that \phi(i)=v_{i} for all i\in[k] and a polynomial \psi(u) of degree \leq(d+1)t such that \psi(i)=w_{i} for all i\in[k]. If \psi(0)=\alpha\bar{\phi}(0), output \phi(0); otherwise, output \bot.*

When the partial results \{(v_{i}, w_{i})\}_{i=1}^{k} are all correctly computed, we have that v_{i}=F(c(i)) and w_{i}=F(c(i))b(i) for all i\in[k]. Then the polynomials \phi(u) and \psi(u), which are interpolated in Verify, will satisfy [(5)](https://ieeexplore.ieee.org/document/#deqn5). The correctness of \Pi_{2} then follows from the facts that \psi(0)=\phi(0)b(0)=\alpha\phi(0) and \phi(0)=F(c(0))=F(\mathbf{x}). The cheating servers may forge a set of partial results which then result in two polynomials \hat{\phi_{\wedge}}(u)\mathrm{and}_{\wedge}\hat{\psi}(u) in Verify. However, without knowing \alpha,\psi(0)=\alpha\phi(0) will be false except with very small probability.

