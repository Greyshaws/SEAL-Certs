SEAL Certification Initiative: Schema & Resolver Design Documentation
=================================================================

Overview
--------

Attestations are issued as proof that a protocol has successfully passed a SEAL security assessment. The Ethereum Attestation Service (EAS) is used to create verifiable, on-chain records of these assessments. This documentation explains the design of the custom resolver contract and EAS schema that powers the certification attestations.

**Certification Types:**
- SCF-Incident Response
- SCF-Multisig Ops
- SCF-Treasury Ops
- SCF-Workspace Security
- SCF-Protocol Deployment and Oversight


### 1\. Custom Resolver Contract: RevocationResolver.sol
-------------

**Contract Address (Sepolia):**
```
0x67a8afe4fe2aa40364e2b1fe7828d51fd8e7d452
```
The resolver is a smart contract that ensures that only SEAL can revoke a certification, even if the attestation was originally submitted by another address (e.g., the certified team).
*   Controls who can revoke attestations
*   Maintains integrity of the certification registry i.e certifications cannot be silently revoked by the cert holder.

### **Key Features**

a) **Immutable Admin Address**

```
address public immutable Admin;
```
*   Set once at deployment
*   Use a multisig wallet for decentralized control
*   Cannot be changed after deployment 
    
b) **Attestation Creation**

```
function onAttest(Attestation calldata, uint256) internal pure override returns (bool) {
    return true;
}
```
*   Anyone can create attestations
*   Supports self-certification model where teams attest to their own compliance
    
c) **Attestation Revocation**

```
function onRevoke(Attestation calldata, uint256) internal view override returns (bool) {
    require(msg.sender == Admin, "Only entity can revoke attestations");
    return true;
}
```
*   Only Admin can revoke
*   Prevents teams from removing certifications after going through assessment
        

### 2\. Schema Design (EAS)
-------------

The schema defines what data is stored inside each attestation. It is registered on the Ethereum Sepolia testnet and this is the [**Schema UID**](https://sepolia.easscan.org/schema/view/0x442fc9dc4f525718595f8bd7a5f6936b5bcf5df25abec9dcfeab365425caaa84)

### Schema Structure

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `Issuer` | string | The name of the organization or audit firm issuing the certification | "Uniswap" |
| `Recipient` | string | The name of the organization or protocol being certified | "Aave" |
| `recipientAddress` | address | Ethereum address of the recipient organization | 0x1234...5678 |
| `certificationType` | string | The type of SEAL certification earned | "SCF-IR-v1.0" |
| `evidenceHash` | string | IPFS hash or other identifier pointing to supporting evidence/documentation (Optional) | "QmX..." |
| `issuanceTimestamp` | uint256 | Unix timestamp when the certification was issued | 1698765432 |

    
### Example Attestation
--------------------------------------

<img width="1198" height="851" alt="image" src="https://github.com/user-attachments/assets/ea9c4ccb-d28f-41eb-83f9-6f77c53410e0" />

- **Version**: 1.0
- **Last Updated**: October 2025
- **Maintained by**: SEAL Certifications Initiative
