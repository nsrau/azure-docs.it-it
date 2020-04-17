---
title: Guida alla risoluzione dei problemi di Crittografia dischi di Azure
description: In questo articolo vengono forniti suggerimenti per la risoluzione dei problemi relativi a Crittografia disco di Microsoft Azure per macchine virtuali Windows.This article provides troubleshooting tips for Microsoft Azure Disk Encryption for Windows VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fafe4a9ef78a92595eaae942fa5d7cbeb2c07189
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458216"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Crittografia dischi di Azure

Questa guida è destinata ai professionisti IT, agli analisti della sicurezza delle informazioni e agli amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure. Questo articolo contiene indicazioni per la risoluzione dei problemi relativi alla crittografia dei dischi.

Prima di eseguire una delle operazioni seguenti, verificare che le macchine virtuali che si sta tentando di crittografare siano tra le dimensioni delle [macchine virtuali e i sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)e che siano soddisfatti tutti i prerequisiti:

- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](disk-encryption-overview.md#group-policy-requirements)
- [Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall

Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie. È quindi possibile che vengano visualizzati messaggi del tipo "Stato estensione non disponibile nella macchina virtuale". In scenari previsti, la crittografia non viene completata. Le sezioni che seguono illustrano alcuni problemi comuni relativi ai firewall che è possibile esaminare.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i [prerequisiti](disk-encryption-overview.md#networking-requirements) di configurazione di rete documentati per la crittografia dei dischi.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault protetto da firewall

Quando la crittografia è abilitata con le [credenziali di Azure AD](disk-encryption-windows-aad.md#), la macchina virtuale di destinazione deve consentire la connettività sia sugli endpoint di Azure Active Directory sia sugli endpoint di Key Vault. Gli endpoint di autenticazione di Azure Active Directory correnti sono mantenuti nelle sezioni 56 e 59 della documentazione [URL e intervalli di indirizzi IP per Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Le istruzioni di Key Vault sono fornite nella documentazione [Accedere ad Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure 
La macchina virtuale deve essere in grado di accedere all'endpoint del [servizio metadati dell'istanza di Azure](../windows/instance-metadata-service.md) che usa un indirizzo IP noto e non instradabile (`169.254.169.254`) accessibile solo dall'interno della macchina virtuale.  Le configurazioni proxy che modificano il traffico HTTP locale a questo indirizzo (ad esempio, l'aggiunta di un'intestazione X-Forwarded-For) non sono supportate.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Risoluzione dei problemi di Server Core di Windows Server 2016

In Server Core di Windows Server 2016, la componente bdehdcfg non è disponibile per impostazione predefinita. Questa componente è necessaria per la Crittografia dischi di Azure. e viene usata per dividere il volume di sistema dal volume del sistema operativo, operazione eseguita solo una volta per la durata della macchina virtuale. Tali file binari non sono necessari durante le operazioni di crittografia successive.

Per risolvere il problema, copiare i quattro file seguenti da una macchina virtuale Windows Server 2016 Data Center nello stesso percorso in Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Immettere il comando seguente:

   ```
   bdehdcfg.exe -target default
   ```

1. Questo comando crea una partizione di sistema da 550 MB. Riavviare il sistema.

1. Usare DiskPart per verificare i volumi, quindi procedere.  

Ad esempio:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Risoluzione dei problemi relativi allo stato della crittografia 

Il portale può visualizzare un disco come crittografato anche dopo che è stato decrittografato all'interno della macchina virtuale.  Ciò può verificarsi quando i comandi di basso livello vengono usati per decrittografare direttamente il disco dall'interno della macchina virtuale, anziché usare i comandi di gestione di Crittografia disco di Azure di livello superiore.  I comandi di livello superiore non solo decrittografano il disco dall'interno della macchina virtuale, ma al di fuori della macchina virtuale aggiornano anche importanti impostazioni di crittografia a livello di piattaforma e le impostazioni di estensione associate alla macchina virtuale.  Se questi non vengono mantenuti allineati, la piattaforma non sarà in grado di segnalare correttamente lo stato di crittografia o il provisioning della macchina virtuale.   

Per disabilitare Crittografia disco di Azure con PowerShell, usare [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguito da [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). L'esecuzione di Remove-AzVMDiskEncryptionExtension prima della disabilitazione della crittografia avrà esito negativo.

Per disabilitare Crittografia disco di Azure con l'interfaccia della riga di comando, usare [az vm encryption disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere gli articoli seguenti:

- [Applicare la crittografia del disco nel Centro sicurezza Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
