# DigiServices
DigiServices is a Cardano-based project that aims to be a trustworthy,
reward-driven, platform for goods and service exchanges

## Table of Contents
* [Tokenomics](#tokenomics)
  * [Supply](#supply)
* [Alice and Bob example](#alice-and-bob-example)
* [White Paper](#white-paper)
  * [Abstract](#abstract)

## Tokenomics

### Supply

Differently from traditional currencies, such as Bitcoin, Litecoin and Ethereum,
DigiServices tokens (DSET) are not deflationary. This is important in order to
incentivise cooperative and honest behaviour in the platform.

A fixed amount of DSET tokens is monthly minted and distributed according to a
Credit Assesment System (CAS). Users receive tokens proportionally to their
scores, obeying the following function:

```haskell
-- The total amount of tokens that will be minted every month
totalAmt :: Int
totalAmt = 1000

-- Review is an integer between 0 and 100
calculateRewards :: [Review] -> [Int]
calculateRewards [] = []
calculateRewards (x:xs) = ((x `div` revSum) * totalAmt `div` 100) : calculateRewards xs
  where
    revSum :: Integer
    revSum = sum xs
```

### Distribution

**Need development**

### Utility

In order to de-incentive dishonest behaviour, DigiServices makes it
possible for service providers to lock an arbitrary amount of tokens (Trust Token, TT) into
the contract proposal in the marketplace and request from the client the same amount. In
this way, there is commitment by both parties and a pledge in case of a conflict.

Thus DSET utility provides the basis for a trustworthy platform by setting a good measure of
reliability through the CAS (Credit Assessment System);furthermore it is an important element
for the decentralized voting mechanism about the platform development.

### Network

Users'activiy is rewarded: inviting new members, acting as a judge, scoring a high CAS provide
benefit for the member and for the platform as a whole. 

## Alice and Bob example

Suppose Alice wants to offer her services as a writer. In normal circumstances
she could search for a publishing company and sign a contract with them.
The problem, in this case, is that natural language contracts is open to
ambiguity and misinterpretation. Additionally, physical contracts draw-backs
are convenience and drafting management.

For Alice an alternative would be to access an online website focused on
freelancer jobs (e.g. Fiverr or Upwork). In this case, alongside with the
ambiguity and flexibility problems (as these websites usually make use of
pre-made natural language contracts), there could be the possibility of Alice
not delivering the project or even of the client not paying the agreed amount.

DigiServices tackles and solves these issues. A Cardano-smart-contract-based 
digital platform enables parties to offer their services avoiding misinterpratation
and ambiguity and leveraging a reputation system that penalizes dishonest parties
and reward honest ones.

Likewise freelancer online websites, through DigiServices Alice would be able to
access a user-friendly web application and publish her service. One of the key 
differences, though, will be the service not to be stored inside a centralized
database; the Datum of a Plutus Validator called 'marketplace' will store it.

The Datum of the marketplace contains a list of `Service`s. `Service` is a special
data type that holds four values: A `Title`, a `Description`, a `Price` and a
`Signature` symbol.

The first value (Title) is the service Alice will provide (e.g. "Novel writer").
The second value (Description) will expand on her background, including an extended 
explanation of what she will do. This content is qualitatively explained and will not
the judge decision in the event of a conflict. The third value (Price) is the amount of DSET tokens the client should pay in order
to receive the service.
The last value (Signature) is a non-fungible token NFT, valid only valid if it
contains a cryptographic signature resulted from the combination of the owner’s
private key and the `Accusation Contract` validator hash.

Example
```haskell
import Ledger

-- Defined explicitily for clarity
signAccusation :: Ledger.Crypto.PrivateKey -> Ledger.ValidatorHash -> Signature
signAccusation pk vh = Ledger.Crypto.sign vh pk

-- This will be stored in the Signature NFT metadata
data Sig = Sig { signatory          :: Ledger.PubKeyHash
               , accusationContract :: Ledger.ValidatorHash
               , signature          :: Signature
               } deriving (Eq, Show)
```

The signature proves that someone agreed with a determined contract.

In our example, Alice would first create an accusation contract. Let’s suppose
she uses Charlie, Daniel and Emma public keys as the list of judges.
Additionally, let’s say the inputs are `“Was a book actually written and
delivered?”`, `“Did it have more than 200 pages”` and `“Was the
client collaborative, providing any information needed?”`. Lastly, let’s
assume the written logic was the following:

```haskell
type ClientTokens = Int
type ProviderTokens = Int
type JudgeTokens = Int

type TTDistribution = (ClientTokens, ProviderTokens, JudgeTokens)
type TotalAmount = Int

distributeTokens :: Bool → Bool → Bool -> TotalAmount → Distribution
distributeTokens inp1 inp2 inp3 totalAmt =
    | (not inp1 || not inp2) && inp3 = ((totalAmt - judgeAmt), 0, judgeAmt)
    | inp1 && inp2 && not inp3 = (0, (totalAmt - judgeAmt), judgeAmt)
    | otherwise = (0, 0, judgeAmt)
  where
    judgeAmt :: Int
    judgeAmt = totalAmt `div` 20
```

Bob could agree with the contract and see that Alice's judges are
reliable and qualified and decide to actually request her services. For that
he would need to provide his signature token and lock the same amount of trust
tokens TT provided by Alice, as well as, the amount of DSET Alice set as her
service price. Let us assume TT + Service fees = 60 DSET.

Supposing Alice is not meeting the agreed terms and decides to write a book with
only 100 pages (against the rules she herself defined and confirmed), Bob could
invoke an "Accuse" event inside the "accusation contract", which would notify the 
first judge in the list (Charlie) and give him a hardcoded fixed deadline (e.g. 24h)
to provide answers to the inputs defined by Alice ("Was a book actually...").

If he does, then the logic will be executed according to the inputs, provided
to each of the terms (e.g. `(True, False, True)`) and would distribute the tokens 
according to the agreed contract terms.
Because of the contract conditions, Alice would receive nothing, Bob 57 TT
and Charlie 3 TT. It is possible, though, that Charlie does not respond within
the deadline. In this case, the next judge in the list will be notified and the
cycle repeat.

Of course, in our example Bob was the one to invoke the accusation, but nothing
stops Alice to do the same in case Bob is not cooperative and does not follow
the agreed rules. DigiServices aims at preventive Non-Payment-Non-Delivery crimes,
assuring through contract conditions a deterministic result for unsatisfactory 
outcomes. This is achieved eliminating ambiguity normally attached to natural
language contracts and providing the ease of use extremely valubale and necessary
in our current society.

## White Paper

### Abstract
The growing need of goods and services exchange require a trustworthy social platform to act as escrow. Even though physical contracts appear to provide a solution, they are often misinterpreted because of their subjective language and they commonly lack the practicality needed in our digital word. We propose a platform that enables parties to create contracts stored in the Cardano blockchain and builds member trust based on token rewards calculated through an algorithm that collects statistics related to the service transactions and returns the amount of tokens per hour that will be rewarded. Parties that refuse to follow the rules pre-defined in the contract will be penalized by losing tokens proportionally to the severity of their misbehavior.
