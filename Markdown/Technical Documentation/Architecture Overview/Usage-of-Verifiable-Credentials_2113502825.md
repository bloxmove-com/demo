<div id="page">

<div id="main" class="aui-page-panel">

<div id="main-header">

<div id="breadcrumb-section">

1.  <span>[BloXmove Dev](index.html)</span>
2.  <span>[Architecture
    Overview](Architecture-Overview_4492492808.html)</span>

</div>

# <span id="title-text"> BloXmove Dev : Usage of Verifiable Credentials </span>

</div>

<div id="content" class="view">

<div class="page-metadata">

Created by <span class="author"> Jan-Paul Buchwald (Unlicensed)</span>,
last modified on Dec 18, 2021

</div>

<div id="main-content" class="wiki-content group">

Attestations in the MBP basic business architecture can be represented
by off-chain verifiable credentials according to the [W3C
specification](https://www.w3.org/TR/vc-data-model/), especially when
their content contains sensitive information that should not be visible
on a ledger.

The following describes the technical flows of verifiable credential
issuance and validation, using the example of user claims
(driverLicense, minAge) that a service consumer gets attested by a KYC
Provider and presents it to the Fleet Node for proving to fulfil the
requirements to rent a car.

<span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2113502825/2141486701.png)</span>

### Issuance: User gets Verifiable Credential(s) from KYC Provider

The flow above assumes the user already has a private/public key pair
and associated DID in the Fleet2Share (F2S) app. The F2S App triggers a
request to get verifications from the Sample KYC Service, passing on the
user DID, some proof data about the claims, and a signature by the
private/public key pair that is associated to the user DID. The Sample
KYC Service resolves the user DID to validate it exists and retrieve the
public key(s) authorised to sign on behalf of this DID. With that
information, the Sample KYC Service can validate the signature.

After successfully validating the real-world proofs, the Sample KYC
Service creates the Verifiable Credentials and adds a proof signed with
a private/public key pair that belongs to the DID of the KYC Provider.
Every VC gets a unique identifier and can be optionally “anchored” in a
Credential Registry Contract on the blockchain, which typically stores
the unique identifier along with the issuer identity and the credential
status information. The issuer can then later update the status of the
credential and thus revoke it.

The VCs are returned to the F2S app, where they are stored in the
wallet.

A VC returned could look like the following (using ethr for the DIDs):

<div class="code panel pdl" style="border-width: 1px;">

<div class="codeContent panelContent pdl">

``` syntaxhighlighter-pre
{
 "@context": [ "https://www.w3.org/2018/credentials/v1" ],
  "type": [ "VerifiableCredential", "DriverLicenseCredential" ],
  // unique identifier of the VC
  "id": "6e90a3e2bf3823e52eceb0f81373eb58b1a0a238965f0d4388ab9ce9ceeddfd3",  
  "issuer": "did:ethr:blxm-dev:0x76fF7A937699D5Fdc02E70B23450deF8F321d3fB",
  "credentialSubject": {
    // id of the main subject of the claim
    "id": "did:ethr:blxm-dev:0xEc1818147dA3B090049d08059966bd6B2E6Cc4F9",
    "driverLicense": true
  },
  "validFrom": "2020-02-25T09:48:57.660Z",
  "proof": { 
    // signature by issuer
    "type": "EcdsaSecp256k1RecoveryMethod2020",
    "created": "2020-02-25T09:48:58.451Z",
    "proofPurpose": "assertionMethod",
    // this marks the public key in the DID document of the issuer to validate the issuer's signature
    "verificationMethod": "did:ethr:blxm-dev:0x76fF7A937699D5Fdc02E70B23450deF8F321d3fB#controller",
    // actual signature value
    "proofValue": "0xe55b271d5ebe3341b8f6d6fcf2468fb5c4308fe121d63ece6f65db93d8ef355e058cd317d81c2f0fb501f41727672ff1d5daa6c90b5f79f79ac28eb3e326cbae1c" 
  } 
}
```

</div>

</div>

### Validation: Fleet Node validates claims

The flow above assumes that the Fleet Node is configured with a list of
trusted issuer DIDs, i.e. the ids of KYC Services or Id Providers the
Fleet Operator trusts.

The validation is done as part of a user request, e.g. the F2S app
requesting the rental of a car. In the payload of this request the F2S
app passes on a Verifiable Presentation of the previously received VCs
signed by the private/public key pair that belongs to the user DID. The
Verifiable Presentation could look like the example below.

<div class="code panel pdl" style="border-width: 1px;">

<div class="codeContent panelContent pdl">

``` syntaxhighlighter-pre
{
  "@context": [ "https://www.w3.org/2018/credentials/v1" ],
  "type": "VerifiablePresentation",
  "verifiableCredential": [
    ...
    // full verifiable credential object from above
    ...
  ],
  "proof": [{
    "type": "EcdsaSecp256k1RecoveryMethod2020",
    "created": "2020-03-18T21:19:10Z",
    "proofPurpose": "authentication",
    // this marks the public key in the DID document of the user to validate the user's signature
    "verificationMethod": "did:ethr:blxm-dev:0x76fF7A937699D5Fdc02E70B23450deF8F321d3fB#controller",
    // user signature value
    "proofValue": "0xab4..."
  }]
}
```

</div>

</div>

The Fleet Node validates the information in the following way:

  - Check if any of the presented VCs is expired

  - For each of the VCs, verify that the issuer id is in the list of
    trusted issuers

  - Resolve user DID, validate existence of user DID and user signature
    in proof of Verifiable Presentation

  - For each of the VCs, resolve the issuer DID, validate existence of
    issuer DID and issuer signature in proof of VC

  - (Optional in case of revocation): For each of the VCs, check the
    Credential Registry Contract for the status, validate it has the
    expected status

If one of the checks or validations fails, the Fleet Node should reject
the request.

### DID Resolver and Credential Registry Contract

The DID Resolver and Credential Registry Contract components mentioned
in the previous description are indicated to make the functionality more
clear. In practice they are both part of the underlying blockchain
network implementation (e.g. Ethereum, Ontology) and should be
abstracted in the Asset Service implementation.

</div>

<div class="pageSection group">

<div class="pageSectionHeader">

## Attachments:

</div>

<div class="greybox" data-align="left">

![](images/icons/bullet_blue.gif) [2020-08-21-Verifiable Credential
Issuance and Validation.png](attachments/2113502825/2113568365.png)
(image/png)  
![](images/icons/bullet_blue.gif) [2020-08-31-Verifiable Credential
Issuance and Validation.png](attachments/2113502825/2141486701.png)
(image/png)  

</div>

</div>

</div>

</div>

<div id="footer" data-role="contentinfo">

<div class="section footer-body">

Document generated by Confluence on Apr 20, 2022 13:42

<div id="footer-logo">

[Atlassian](http://www.atlassian.com/)

</div>

</div>

</div>

</div>
