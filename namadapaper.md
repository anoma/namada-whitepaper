---
title: 'Namada : The public goods privacy blockchain #TODO insert relevant catchphrase'
author: Bengt Lofgren, Christopher Goes, Awa Sun Yin, Adrian Brink
fontsize: 9pt
date: \textit{Pre-release, \today}
abstract: |
	Namada is the first fractal instance of the Anoma ecosystem. Namada is an IBC compatible blockchain that employs a multi-asset shileded pool. The multi-asset shielded pool allows for users to make shileded transfers of arbitrary assets and is an extension of the Sapling circuit used by Zcash/Zerocash. Namada uses the Tendermint consensus and implements a canonical and trustless bridge to the Ethereum ecosystem.


urlcolor: cyan
bibliography: namadapaper.bib
classoption:
    - twocolumn
header-includes:
    - \usepackage{fancyhdr}
    - \usepackage{graphicx}
    - \usepackage{supertabular}
    - \usepackage{booktabs}
    - \usepackage{array}
    - \usepackage{tabularx}
    - \pagestyle{plain}
    - \fancyhead[RE,LO]{}
    - \fancyhead[LE,Ro]{}
    - \fancyhead[CO,CE]{}
    - \fancyfoot[CO,CE]{}
    - \fancyfoot[LE,RO]{\thepage}
---
# Clearing up the confusion
## Anoma or Namada?
**Anoma** is an ecosystem architecture that facilitates the operation of networked *fractal instances*. An example of a fractal instance is a sovereign blockchain that incorporates the Anoma consensus and execution model is **Namada**.
# Background and motivations

Transparency and privacy are two ends of a double edged sword. Transparency enables trust by enforcing easily-verifiable honesty, but fundamentally limits expressiveness. Any activity that cannot be publicised globally and permanently becomes inevitably supressed. Granted, for a subset of activity, this suppression may come as a feature, rather than a bug, as it forces illegal and malicious off the system. On the other hand, an arguably large subset is caught in the crossfire, ranging from medical records and political opinions to everyday transactions. As soon as there exists **one** party from which information must be withheld (on a global scale), a fully transparent model becomes unviable. We present Namada, an open-source, privacy-focused, blockchain that provides in-built optional privacy for users. Namada inherits the properties of a transparent system in being verifiable, trustless and censorship-resistant at a global scale, without sacrificing expressiveness by guaranteeing privacy by default. In this paper, we first outline the features of Namada. We then outline the stack and architecture in more detail, including high-leveled explanations for the cryptography involved. We conclude with the future roadmap and how we envisage Namada evolving.

## The importance of privacy in today's economy
As computational power and data storage improves, the world realises that there is a large amount of valuable information that we, whether we would like to or not, emit. This information has many use cases. On one hand, the information can be used to predict and protect us from pandemics, to learn about our biases, and help direct more relevant ads. But as we have learnt, it can also be used in order to influence our political decisions, collect and distribute our healthcare data without our consent, and help direct more relevant ads. Our transactions in particular, are being monitored increasingly. We do not believe that this is necessarily a bad thing. However, we do believe that we, as people, should have the discretion of who is able to collect and monitor the information we emit (and what information we emit). This is the primary motivation for Namada.
[//]: # (TODO: CITE CITE CITE)

## Privacy as a public good

We believe that privacy should be subsidised. We motivate this opinion in the following section.

In contemporary economics, a good is classified as a *public good* if it is **non-rivalrous** and **non-excludable**. Put simply, a good is non-rivalrous if its consumption by one person does not prevent its consumption by another person. A good is called non-excludable if its consumption cannot be prevented by any person once it exists. 

Strictly speaking, privacy itself is not a public good because although it is not rivalrous it *is* excludable. Preventing users from entering the shielded pool is trivial. However, it nonetheless exhibits a property commonly associated with public goods, namely having positive externalities. This occurs when one person's consumption of the good benefits another person, and this positive externality is indeed non-excludable. More concretely, when one user enters the shielded pool, it increases the total privacy for everyone within the shielded pool already, and it is impossible to exclude anyone already in the shielded pool from benefiting from it.

### Privacy's Positive Externality

The positive externality can be explained with a toy example (and accompanying diagram).

For the sake of simplicity, assume each "agent" in the economy is identical in terms of their preferences. We assume that the user values the opportunity to exist in a privacy set, and that the value of teh privacy set increases as the privacy set grows in size. Trivially, a privacy set of 0 people is worth nothing. Further, we assume that each additional increase in the size of the privacy set has a "decreasing marginal benefit" property, in the sense that each additional user contributes less to the overall privacy as a whole. As the privacy set grows infinitely large, the additional benefit of having someone enter the set becomes negligible. In economics, we tend to represent this through a "utility" function, that simply exists in order to measure cost and value for the agent. A natural choice for a utility function that exhibits the above properties is given by $$ U(n) = \ln (n) $$, where $n$ is the size of the privacy set. Although this is discrete, for simplicity, we will work in the continuous domain.


Additionally, we assume that there exists some unavoidable "cost" $c$ to the user for entering the privacy set. In the real world, this can correspond to learning about zero-knowledge cryptography, handling private keys, and other forms of "effort" and risks that the user may take along the journey. 

![privacy_econ](./privacy_economics.png)

Because of the positive externality associated with entering the privacy set, there is "unrealised value" that is lost in the economy if users are unable to coordinate. Whilst no other user is in the privacy set, the value of the privacy set is 0, In contemporary economics, this "lost value" (shown by the shaded area between the value and the cost of entering) is referred to as "deadweight loss".

![deadweight loss](./privacy_economics_2.png)

Therefore, if the protocol can incentivise a number of users (with ample sizes of assets) to enter the privacy set such that there is sufficient value in entering the privacy set, the coordination problem is solved. If the "social planner" was in full knowledge of exactly the amount of users needed in order to achieve this value $n^*$, she could offer exactly the correct amount of subsidy to incentivise the first $n^*$ users to use the protocol and nothing more.

### Correcting the externality

We suggest an alternative approach, whereby we can claim that:

If the subsidy $s(n)$ is such that $s(n) \propto \frac{1}{n}$, then for a sufficiently large constant of proportionality, the subsidy will incentivise the correct number of users to join the system. Additionally, this incentive scheme comes with the added benefit of being finite and predictable. This is not the only possible solution, but is one of them and seems natural.

![Unoptimised solution](./privacy_economics_3.png)

The above subsidy is not sufficient in incentivising users to join the network, although it does lower the threshold slightly. In the above example, the size of the privacy set would increase from 0 to ~0.6. In order to reach the critical mass, we need to incentivise for a privacy set of at least size $n \approx 5.4$

If we increase the incentive to be exactly proportional to the cost for the user, it suffices. An additional nice property with desigining the subsidy in this way is that the subsidy becomes easily interpretable; the total subsidy is exactly the cost to any single user, distributed across all users.

![Unoptimised solution](privacy_economics_sol.png)

Now the point minimising the areas between the curves is given by $ \ln(x+1) - c + \frac{k}{x} = 0$ (The derivative of the integral).

This gives a set of valid points. Now, additionally, ideally, the solution is tangential to the utility curve at the intersection.


$\frac{\partial}{\partial x} \ln(x+1) = -\frac{\partial}{\partial x}\frac{k}{x} \Leftrightarrow x^2 -kx - k = 0$

These two simultaneous equations can be solved for $k$. Alternatively, one can solve the first equation, but forcing the determinant to be zero. These two methods should give equivalent solutions.

# User Features

## Validity Predicates (VPs)

Validity Predicates are the backbone of Anoma-based blockchains.
In general, validity predicates specify which state changes relevant to state that it "owns" are valid in the blockchain. "Ownership" in this sense is an overloaded term, and is borrowed from its use in programming languages, where a variable can be "owned" over the runtime of a program. Validity predicates generally have "ownership" over state that it has created. For User Validity Predicates, this involves all the references to the Validity Predicates address. 

In Namada, a user can modify their validity predicate in order to have control over:

1. Account addresses that are allowed to be involved in asset transfers with the user
2. Token addresses that may attach a balance to the user's account address (i.e what tokens can be received by the user)
3. TODO: More functionality?

*Note that unlike Anoma-based blockchains in general, all Namada validity predicates are transparent.

Validity predicates grant users functionality that is usually unavailable in other blockchains due to fundamental design choices in the VM. This functionality allows users to easily blacklist and whitelist both users and tokens. An important and relevant use case for this functionality is complying with regulatory requirements. 

## Multi Asset Shielded Pool (MASP)

The Multi Asset Shielded Pool (MASP) is the primary feature that Namada offers. The MASP is inspired by the work produced by the Electric Coin Company, and implements the ideas of the Sapling Circuit directly. We acknowledge the importance of this work and therefore propose to offer the ECC team tokens of appreciation at our genesis block.


At a very high level[^1], the MASP works in the following fashion:

There exists two separate sets:
1. The shielded set
2. The transparent set

### Transparent set
From a user perspective, all tokens held in the transparent set are equivalent to holding tokens in any other transparent ecosystem. The functionality available to users are transfering assets from one address to another address, as well as bridging assets across the bridges deployed on Namada.

### Shielded Set

#### **Shielding assets**

Assets are minted into the shielded set from a valid zero knowledge proof that produces a "note".
The zero knowledge proof is meant to prove a set of things:
1. The base currency in which the note will be denominated
2. The value of the currency 
3. The address to which the note is owned
4. The epoch in which the note was minted [^2]

The underlying asset is sent to escrow.

[^1]: For more details, see TODO CITE ZeroCash Paper @

[^2]: The reason for this has to do with providing rewards for contributing to the shielded set

#### **Transferring Assets**
Notes are transferred by constructing zero knowledge proofs that are able to nullify notes and mint them under the ownership of the receiver.

#### **Unshielding assets**
The user unshielding assets publicly reveals the information used in the minting process, in addition to its nullifier. Once this information is verified, the user is able to take ownership of the equivalent asset in the escrow contract, in addition to any allocated awards from the Convert Circuit [^3].

[^3]: 
See the relevant section for more information on the Convert Circuit

## Convert Circuit (CC)
The Convert Circuit is a special feature added to the Sapling Circuit, which is handled by a native Validity Predicate. 

The CC comes into action at the withdrawal of shielded assets. At the time of withdrawal, the user reveals the epoch in which the underlying note was minted. This is used in order to calculate the relevant NAM tokens that are distributed as part of Namada's subsidy for holding assets in the privacy set.

#TODO: Add Formula


## Trustless Bridges
We recognise that having secure, isolated bridges is a vital component in paving the way towards a multi-chain future. Given the risks associated with Bridges, we believe that users should not need to place additional trust assumptions when interacting with them. Unlike when using third-party bridges, the user will only need to trust the validators on Namada as well as the Namada on the originating chain of the asset. These are unavoidable trust assumptions. Because of the lack of additional trust assumptions, we call these briges *trustless*. 

Trust assumptions when using bridges in general:

$\text{Trust System} = \min(\text{Trust}(\text{Bridge Operators},\text{Trust}(\text{Source Chain}), \text{Trust}(\text{Destination Chain}) \Rightarrow \\
=  \min(\text{Trust}(\text{Source Chain}), \text{Trust}(\text{Destination Chain})$

More specifically, take Ethereum bridge as an example. Validators on Namada will be running Ethereum full nodes, and will be tasked with listening for relevant events on the governance-specified bridge contract. The block proposer will then include the relevant transaction on the Namada consensus process. Once consensus threshold has been reached on validating the deposit, the block proposer is incentivised to include a transaction that will allow the user to "mint" a corresponding value of equivalent asset on the Namada side.

Similarly, the reverse process will allow for withdrawals of deposits on the Ethereum side, whereby validators are tasked with initialising the transaction that allows the user to withdraw the correct amount of asset from the Ethereum bridge contract.


# Governance

## Public Goods Funding (PGF)
Namada's existence relies on its open-source software stack, research, and ecosystem tooling all of which have relied on standing on the shoulders of other open-source information, readily available to the public through the internet. All of these foundations share the common attribute of being non-rivalrous and non-excludable, which are sufficient charactersitics to define them as public goods.

It is a well known concept in the economics literature that public goods tend to be underfunded due to these qualities. In short, this occurs because of coordination problems between individuals, whereby equilibrium market prices do not reflect the benefit the goods existence brings to the society as a whole, but rather the sole buyer of the good. 

One proposed solution to avoid such coordination failures is to subsidise such goods in a way that allows individual incentives to align with that of the public.

There will be a periodically elected council tasked with voting on projects/goods that should be funded.

The council is responsible for two types of public goods funding.

1. Continuous funding - in order to fund ongoing public goods projects in order to assist their coming to fruition
2. Retroactive funding - in order to fund completed and easily evaluated public goods, in order to incentivise future public goods to be built

Funding public goods is sourced through native asset (NAM) inflation, dictated by the protocol.

At the time of writing, 10% annual inflation is dedicated towards PGF.

## Retroactive PGF
Retroactive public goods funding has become an increasingly popular method for funding public goods. By retroactive, we mean to say funding goods in a post-fruition fashion. Goods receive a lump-sum subsidy in proportion to their perceived social utility at some point in time in which the perceived utility is believed to be more aligned with its potential social utility.

The benefit of this method of PGF is that it is often more clear to evaluate the benefit public goods have brought to society after-the-fact.  Because of the nature of public goods being non-excludable, its benefit to society is a function of the number of people that use it, which may not be obvious at first. Especially for public goods that have utility which is multiplicative to the number of people that make use of it (e.g the internet), retroactive funding is an especially useful tool. In this way, uncertainty is factored out in an important way.

The cons with retroactive PGF are two-fold. 

1. Retroactive PGF only allows for ex-post assessment of projects at the time the assessment is being made. This means that any public good that was unable to come to fruition because of a lack of funding or other circumstances, as well as any counterfactual of the good not existing, is ignored


2. Increased trust assumptions. In order for rPGF to correctly function as an incentive mechanism for the entrepreneur (agent) to take on risk in diverting resources to the project, the agent must be confident that the rPGF assessment mechanism will both work correctly and exist in the future at the time the assessment will be made. This may be able to be offset by some additional reward, but with risk-averse agents, this reward will have to be inefficiently allocated.

### Figuring out the right amount of funding

We will assume an economic agent that is both rational and risk-neutral. This means that the agent is trying to maximize the reward in expectation. A risk-averse agent will inevitably require more funding.

We will assume a risk-free interest rate $r$ that remains constant over time. This is a simplifying assumption that only affects the complexity of the equation.

The agent is considering investing in a public goods project. The agent assesses the probability of the project being successfully funded by the rPGF council at some rate $0<s<1$. Note that the success rate $s$ includes both 
1. the inherent risk of the project itself as well as 
2. the risk attached to the rPGF council existing and functioning properly at the time of assessment. 

The project comes at one-time sunk cost $C$ (which includes the opportunity cost to the agent of choosing this endeavour over any other).

Additionally we assume that the ex-post benefit to society $U$ at the time of assessment $T$ is greater than the cost of investing in the project (so that the rPGF council will find it worth funding ex-post). 

$$U > C(1+r)^T$$

An agent will be incentivised to invest in their project if funding retroactively directed to the project $F$ is at least as high as their costs.

$$ sF \geq C(1+r)^T \Rightarrow F \geq \frac{C}{s}(1+r)^T $$

If the agent is risk-averse, then this becomes more complicated. A common way to measure risk aversion is to assign the agent a utility function
$$u(c) = \begin{cases}
\frac{c^{1-\eta}}{1-\eta} &\text{if }\eta \geq 0 \text{ and } \eta \neq 1\\
\ln(c) &\text{if } \eta = 1
\end{cases}$$
(known as [isoelastic utility](https://en.wikipedia.org/wiki/Isoelastic_utility)).
![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/50/Isoelastic.svg/440px-Isoelastic.svg.png)

In non-nonsense speak, this basically means that the agent is sensitive to uncertainty, and this risk-averseness is captured by $0\leq \eta \;\; ; \eta \neq 1$—the bigger $\eta$, the bigger the aversity to risk. For the case $\eta = 1$, the effect is that doubling the expected reward increases the utility of the risk-awerse agent only by a additive constant. 

Then we have 
$$ \frac{(sF)^{1-\eta}}{1-\eta} \geq C(1+r)^T \Rightarrow F \geq \frac{1}{s}\big((1-\eta)C(1+r)^T\big)^\frac{1}{1-\eta}$$

There is an extensive literature in estimating $\rho$, and estimates vary wildly. [Groom and Maddison](https://link.springer.com/article/10.1007/s10640-018-0242-z) estimated the value to be around 1.5 for the United Kingdom.

As $s$ approaches $1$, we begin to move towards a world where retroactive funding achieves all the benefits of continuous funding, whilst also maintaining its advantage of filtering uncertainty at no additional premium (since if F is large enough this is true regardless). The premium needed whilst $s<1$ will depend on the risk-averseness of the agent, and could be mitigated by the existence of insurance, should a market for that exist.






## Continuous PGF

Continuous PGF is meant to assist public goods coming to fruition. Every quarter, the council votes and decides on a group of public good projects that will receive up to 5% of the annual inflation distributed continuously over that quarter. The benefit of continuous funding include but is not limited to:

1. Since the money is received up front, the entrepeneural agent is less concerned with the possibility of not receiving funding despite the success of the project. Therefore it comes at a lower 

2. In an economy where the entrepreneurial agent is unable to take loans at a reasonable price (either because lenders do not have enough faith in the rPGF system or because the agent is unable to provide sufficient collateral), an agent that would be unable to pursue the endeavour is more likely to pursue it.

On the other hand, there are inevitable costs:

1. The council takes on additional risk, since the benefit of the good is less clear without the benefit of hindsight. 
2. A lack of incentive for commitment by the agent, which can lead to uncompleted projects. 
3. Increased uncertainty in evaluating the council's decisions as speculation is harder to assess. This places greater trust assumptions on the council acting faithfully.



## Four goals of PGF
Public goods funding is intended to be directed to the four following categories.

For development related to Namada/Anoma ecosystem:

1. Technical research related to Namada/Anoma, e.g research in cryptography, distributed systems, consensus mechanisms, 

2. Engineering related to Namada/Anoma, including but not limited to the user-interface, storage/throughput optimisation, bug-fixes and can be distrubted through e.g developer grants, bug bounties, etc 

3. Social reserach related to Namada/Anoma, more specifically resarch exploring the relationship by the technology and humans. This may be how privacy affects interactions e.g artist grants etc

For public goods not directly related to Namada/Anoma:

4. External public goods - e.g carbon capture, independent journalism, direct cash transfers 

More detailed information can be found at specs.namada.net/economics/public-goods-funding.html. 

# Stack and Architectural Details

The codebase is built in rust. As with any Anoma-based blockchain, validity predicates dictate state changes. The MASP is built using the Sapling circuit as introduced by the Electric Coin Company (ECC).

## Consensus

Namada is built with the Tendermint BFT consensus algorithm. This not only allows Namada to achieve fast-finality with up to 150 validators, but also equips Namada with interopability between other tendermint-based blockchains (such as the Cosmos ecosystem).

## Cubic Slashing

An important feature that Namada implements 

# Future Roadmap

## Private Bridges

Imagine two different bridges exist between blockchain A and blockchain B, and there exists some fungible asset FUN originating on blockchain A. In general, these two bridges will have separate security assumptions. Therefore, despite being fungible on blockchain A, FUN sent over the first bridge (name it foo), will not be fungible with FUN sent over the other bridge (name it bar). Instead, on blockchain B we have FUN-foo and FUN-bar. A common solution to dealing with this problem (see HOP protocol and others), is to first agree on a "canonical" bridge, and then have liquidity pools that exist on blockchain B to convert all non-canonical bridged assets to its canonical counterpart by in essense "reverting" the actions of the bridge. In practice, say bridge Foo is decided on as the canonical bridge, then all assets bridged through Bar will eventually have to be bridged back from blockchain B to blockchain A through Bar, in order to be sent through bridge Foo. If sufficient liquidity providers exist on Blockchain B to take on this task for a fee, the user experience is not affected.

![bridges_today](bridges.png)

When working with a shielded pool, the additional factor of privacy set size comes into play. If a small size of FUN is being routed through Bar compared to Foo, then any asset routed through Foo will have stronger privacy guarantees than Bar, and will eventually lead to equilibria whereby a canonical bridge is inevitable. Further, any liquidity provider swapping assets from non-canonical bridges to the canonical-bridge asset lose all privacy guarantees.

Ideally, this can be avoided by incorporating *private* bridges. Under a private bridge framework, the amount as well as the underlying asset can remain private. This feat is achieved through multiparty computation (MPC).

## Privacy-Conserving Swaps

Using the platform developed by Osmosis-Labs, Namada will allow for privacy-preserving swaps between assets.

The Uniswap V3 contract will be integrated for similar purposes.

A user on Namada (Alice) will be able to transfer her asset on Namada in exchange for another asset on the destination chain by sending a readable message encoding this desire. The swap up to this point in time is therefore transparent, but the swapped asset is returned to a native VP Namada. The native VP will escrow the asset by first shielding it, and then creating a shielded transfer of the asset to Alice.

In this way, privacy of the swap is conserved for Alice from the point the swap has occurred. 
