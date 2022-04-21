<div id="page">

<div id="main" class="aui-page-panel">

<div id="main-header">

<div id="breadcrumb-section">

1.  <span>[BloXmove Dev](index.html)</span>
2.  <span>[Architecture
    Overview](Architecture-Overview_4492492808.html)</span>

</div>

# <span id="title-text"> BloXmove Dev : Universal Asset Service </span>

</div>

<div id="content" class="view">

<div class="page-metadata">

Created by <span class="author"> Jan-Paul Buchwald (Unlicensed)</span>,
last modified on Dec 18, 2021

</div>

<div id="main-content" class="wiki-content group">

## Using ONT ID for Service Consumer, Ethr DIDs for Other Components

The following assumes a scenario where the user / consumer of the rental
service has an ONT Id, and all other entities Fleet Owner (with the
Fleet Node backend service acting on its behalf), Vehicle, and Rental
Contract are using Ethr DIDs. Further assumed is that the user uses a
mobile app where the private key and certain VCs of its ONT Id are
already stored, e.g. the Welcome Home App or a more general wallet app
like ONTO.

The basic onboarding flow is then handled in the frontend generating a
new ONT private/public key pair in the Fleet2Share app, then jumping to
an ONT App to ask the new public key to be added to the existing ONT ID
and the VCs to be returned to the Fleet2Share app, where the ONT ID and
VCs are stored for later usage.

<span class="confluence-embedded-file-wrapper image-center-wrapper">![](attachments/2234351276/4498260048.png)</span>

<span class="confluence-embedded-file-wrapper">[![](attachments/thumbnails/2234351276/4498227256)](attachments/2234351276/4498227256.rtf)</span>

After the initial onboarding, the F2S App uses the ONT ID to request a
rental, presenting the claims in a Verifiable Presentation to the Fleet
Node. Using the Universal Asset Service, the Fleet Node recognises that
the consumer uses an ONT ID and does the necessary validations and
checks towards the ONT Network. The rental contract is handled on the
Ethereum Network as the layer 1 of the service provider. When it gets to
confirming the contract by the user, the F2S App creates a VC with the
contract DID and the topic “consumerConfirm” as the subject issued by
the ONT ID. This VC is then sent to the Fleet Node and stored as an
attestation on the Ethereum Network Attestation Registry Smart Contract.

The same way, the remaining parts of the rental process are handled,
with the F2S App always using the ONT ID for consumer confirmations or
signatures, and the Fleet Node and Virtual Car Wallet recognising the
ONT related DID based on the Universal Asset Service implementation.

## Asset Service Interface

The current asset service TypeScript definition can be found in GitHub:
<https://github.com/bloxmove-com/did-asset-library/blob/master/src/services/asset.service.ts>

The following table lists the current operations and provides a basic
specification how a support of multiple/different DID methods should be
achieved.

It is assumed that the configuration of the component using the asset
service (e.g. a backend service) must point to a particular account and
DID, thus defining which DID method and related ledger network the
“current account” uses.

<div class="table-wrap">

<table>
<thead>
<tr class="header">
<th><p><strong>Operation</strong></p></th>
<th><p><strong>Specification</strong></p></th>
<th><p><strong>Prio</strong></p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>init()</p></td>
<td><p>Calls init for all supported DID methods that require an initialization</p></td>
<td><p>1</p></td>
</tr>
<tr class="even">
<td><p>createAsset(assetName, initialDataProperties, assetType?) → new asset DID</p></td>
<td><p>Asset created in same network as current account DID</p></td>
<td><p>2</p></td>
</tr>
<tr class="odd">
<td><p>addInvolvedParties(assetDID, involvedPartiesDIDs, permissions)<br />
<span style="color: rgb(54,179,126);">addInvolvedPartiesByPublicKey(assetDID, involvedPartiesPublicKeys, permissions)</span></p></td>
<td><p>DID method of current account, asset and involved party must be the same, uses network according to this DID method<br />
<span style="color: rgb(54,179,126);">New variant to add involved parties based on their public key</span><br />
<span style="color: rgb(54,179,126);">Interface of permissions attribute is extended to contain different attributes of the DID document the involved party is added to (e.g. </span><code>publicKey</code><span style="color: rgb(54,179,126);">, </span><code>authentication</code><span style="color: rgb(54,179,126);">, </span><code>controller</code><span style="color: rgb(54,179,126);">, `</span><code>recovery</code><span style="color: rgb(54,179,126);">)</span></p></td>
<td><p>3</p></td>
</tr>
<tr class="even">
<td><p>getInvolvedParties(assetDID) → DIDs<br />
getOwner(assetDID) → DID<br />
getDataProperty(assetDID, key, assetType?) → string</p></td>
<td><p>Uses network according to DID method of asset DID</p></td>
<td><p>3</p></td>
</tr>
<tr class="odd">
<td><p>createDataProperty(assetDID, key, value, assetType?, changable?)<br />
updateDataProperty(assetDID, key, value, assetType?)</p></td>
<td><p>DID method of current account and asset must be the same, uses network according to this DID method</p></td>
<td><p>3</p></td>
</tr>
<tr class="even">
<td><p>getAttestations(assetDID, topic) → list of attestation objects</p></td>
<td><p>Uses network according to DID method of asset DID</p></td>
<td><p>1*</p></td>
</tr>
<tr class="odd">
<td><p>setAttestation(assetDID, topic, externalVC)</p></td>
<td><p>DID method of current account and asset must be the same, this determines the network where the attestation is stored<br />
</p></td>
<td><p>3</p></td>
</tr>
<tr class="even">
<td><p>resolveName(name) → DID<br />
setName(name, assetDID)</p></td>
<td><p><span style="color: rgb(54,179,126);">Uses a global name service or namespace defined and governed by the Mobility Blockchain Platform that supports arbitrary name ↔︎ DID mappings, could be even made off-chain or permissioned for data privacy reasons</span></p></td>
<td><p>3</p></td>
</tr>
<tr class="odd">
<td><p>verifySignature(signerDID, message, signature) → boolean</p></td>
<td><p>Uses (default) signature method based on signer DID</p></td>
<td><p>1</p></td>
</tr>
<tr class="even">
<td><p>signMessage(message) → string</p></td>
<td><p>Uses (default) signature and DID method of current account</p></td>
<td><p>1</p></td>
</tr>
<tr class="odd">
<td><p>isValidDID(assetDID) → boolean<br />
getDIDDocument(assetDID) → JSON<br />
setDIDDocument(didDocument)</p></td>
<td><p>Uses network according to DID method of asset DID</p></td>
<td><p>2</p></td>
</tr>
<tr class="even">
<td><p>createVerifiableCredential(type, credentialSubject, expirationDate<span style="color: rgb(54,179,126);">?</span>, proofPurpose?) → VC <span style="color: rgb(54,179,126);">| string</span><br />
createVerifiablePresentation(VC[] <span style="color: rgb(54,179,126);">| string[]</span>, <span style="color: rgb(54,179,126);">expirationDate?</span>, proofPurpose?) → VP <span style="color: rgb(54,179,126);">| string</span></p></td>
<td><p>Uses DID method of current account for the signer of the VC or VP<span style="color: rgb(54,179,126);">, must support both JSON-LD (object) or JWT (string) VC and VP formats</span></p></td>
<td><p>1</p></td>
</tr>
<tr class="odd">
<td><p><span style="color: rgb(255,86,48);"><del>isVerifiablePresentationValid(signerDID, VP) → boolean</del></span></p></td>
<td><p><span style="color: rgb(255,86,48);">Removed in favor of validateVerifiablePresentation (see below)</span></p></td>
<td><p>1</p></td>
</tr>
<tr class="even">
<td><p><span style="color: rgb(54,179,126);">validateVerifiableCredential(VC | string) → VC | null</span></p></td>
<td><p><span style="color: rgb(54,179,126);">Validates the VC (can be both JSON-LD or JWT format) in terms of expiration, signature, and returns the VC object if the information is valid</span></p></td>
<td><p>1</p></td>
</tr>
<tr class="odd">
<td><p><span style="color: rgb(54,179,126);">validateVerifiablePresentation(VP | string, expectedSignerDID) → VC[]</span></p></td>
<td><p><span style="color: rgb(54,179,126);">Validates the VP (can be both JSON-LD or JWT format) in terms of expiration and signatures, returns list of contained VC objects if valid</span></p></td>
<td><p>1</p></td>
</tr>
</tbody>
</table>

</div>

\*priority only relates to the Ethereum network

All priority 1 elements have been implemented as part of the moveX@ONT
scenario for the Startup Autobahn Expo Day in September 2020. Priority 2
and 3 elements are implemented in the Universal Asset Service, but
mostly missing the implementation in the Ontology Asset Service and thus
would not work if called for an ONT DID.

</div>

<div class="pageSection group">

<div class="pageSectionHeader">

## Attachments:

</div>

<div class="greybox" data-align="left">

![](images/icons/bullet_blue.gif) [2020-08-21-Verifiable Credential
Issuance and Validation.png](attachments/2234351276/2234318497.png)
(image/png)  
![](images/icons/bullet_blue.gif) [ONT Wallet in F2S
App.png](attachments/2234351276/2245525097.png) (image/png)  
![](images/icons/bullet_blue.gif) [2021-12-18-ONT Wallet in F2S
App.png](attachments/2234351276/4498260048.png) (image/png)  
![](images/icons/bullet_blue.gif) [websequencediagrams-ONT Wallet in F2S
App.rtf](attachments/2234351276/4498227256.rtf) (text/rtf)  

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
