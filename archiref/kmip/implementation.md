---

copyright:

  years:  2016, 2020

lastupdated: "2020-04-03"

subcollection: vmware-solutions


---

{:external: target="_blank" .external}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}

# KMIP for VMware implementation and management
{: #kmip-implementation}

## Planning
{: #kmip-implementation-planning}

There is no charge for the KMIP for VMware service. To review the Key Protect and Hyper Protect Crypto Services pricing plans, see the [Key Protect](https://cloud.ibm.com/catalog/services/key-protect) and [Hyper Protect Crypto Services](https://cloud.ibm.com/catalog/services/hyper-protect-crypto-services) catalog pages.

If you are using vSAN encryption, plan to consume one root key in Key Protect or Hyper Protect Crypto Services, plus two standard keys for each vSAN cluster that you encrypt.

If you are using vSphere encryption, plan to consume one root key, plus one standard key per vSphere cluster, plus one standard key per encrypted virtual machine.

## Connecting the key management server
{: #kmip-implementation-connecting-kms}

To enable vSphere encryption or vSAN encryption by using KMIP for VMware, you need to complete the following tasks:

1. [Enable service endpoints in your account](/docs/account?topic=account-vrf-service-endpoint#service-endpoint).
2. Create a key manager instance, by using either [IBM Key Protect](/docs/key-protect?topic=key-protect-getting-started-tutorial) or [{{site.data.keyword.cloud_notm}} Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-get-started#get-started). If you are using Hyper Protect Crypto Services, be sure to [initialize your crypto instance](/docs/hs-crypto?topic=hs-crypto-initialize-hsm#initialize-hsm) so that Hyper Protect Crypto Services can provide key related functions.
3. Create a customer root key (CRK) within your key manager instance.
4. Create an Identity and Access Management (IAM) [service ID and API key](/docs/iam?topic=iam-serviceidapikeys) for use with KMIP for VMware. Grant this service ID platform viewer access and service write access to your key manager instance.
5. [Create a KMIP for VMware instance](/docs/vmwaresolutions?topic=vmware-solutions-kmip_standalone_ordering) from the {{site.data.keyword.vmwaresolutions_short}} console.
6. Within VMware vCenter, create a key management server (KMS) cluster with two servers, one for each KMIP for VMware endpoint in your chosen region.
7. Select one of VMware&rsquo;s methods to generate or install a KMS client certificate in vCenter.
8. Export the public version of the certificate and configure it as an allowed client certificate in your KMIP for VMware instance.

## Enabling encryption
{: #kmip-implementation-enable-encrypt}

To use vSAN encryption, edit the vSAN general settings in your vCenter cluster and select the encryption checkbox.

The vSAN health check might send periodic warnings that it is unable to connect to the KMS cluster from one or more of your vSphere hosts. These warnings occur because the vSAN health check connection times out too quickly. You can ignore these warnings. For more information, see [vSAN KMS Health Check intermittently fails with SSL Handshake Timeout error](https://kb.vmware.com/s/article/67115){:external}.
{:note}

To use vSphere encryption, edit your virtual machine storage policies to require disk encryption.

## Key rotation
{: #kmip-implementation-key-rotation}

Rotate your [Key Protect](/docs/key-protect?topic=key-protect-rotate-keys#rotate-keys) or [Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-rotate-keys) customer root key (CRK) by using the {{site.data.keyword.cloud_notm}} console or API.

For VMware vSAN encryption, rotate your VMware key&ndash;encrypting keys (KEKs) and optionally data&ndash;encrypting keys (DEKs) from the vSAN general settings in your vCenter cluster.

For VMware vSphere encryption, rotate your VMware KEKs and DEKs (optionally) by using the **Set-VMEncryptionKey** PowerShell command.

## Key revocation
{: #kmip-implementation-key-revocation}

You can revoke all keys in use by KMIP for VMware by deleting your chosen CRK from your key manager.

When keys are revoked, all data that is protected by these keys and by your KMIP for VMware instance is cryptographically shredded by this method. VMware preserves some keys while an ESXi host is powered on, so you need to restart your vSphere cluster to ensure that all encrypted data is no longer in use.
{:important}

KMIP for VMware stores individual wrapped KEKs in your Key Protect or Hyper Protect Crypto Services instance by using names that are associated with the key IDs that are known to VMware. You can delete individual keys to revoke the encryption of individual disks or drives.

VMware does not delete keys from the KMS when a VM having encrypted disks is removed from inventory. This is to allow recovery of that VM from backup or if it is restored to inventory. If you want to reclaim these keys and cryptographically invalidate all backups, you need to delete the keys from your key manager instance after you delete your VMs.
{:note}

## Related links
{: #kmip-implementation-related}

* [Solution overview](/docs/vmwaresolutions?topic=vmware-solutions-kmip-overview)
* [Solution design](/docs/vmwaresolutions?topic=vmware-solutions-kmip-design)
* [IBM Key Protect](/docs/key-protect?topic=key-protect-getting-started-tutorial)
* [{{site.data.keyword.cloud_notm}} Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-get-started#get-started)
