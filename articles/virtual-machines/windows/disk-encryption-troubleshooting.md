---
title: Guida alla risoluzione dei problemi di Crittografia dischi di Azure
description: Questo articolo offre suggerimenti per la risoluzione dei problemi di Crittografia dischi di Microsoft Azure per macchine virtuali Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e48c6cfd1160406d55ffdc3c9bafe733a6e5e4a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400078"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Crittografia dischi di Azure

Questa guida è destinata ai professionisti IT, agli analisti della sicurezza delle informazioni e agli amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure. Questo articolo contiene indicazioni per la risoluzione dei problemi relativi alla crittografia dei dischi.

Prima di eseguire le operazioni seguenti, assicurarsi che le macchine virtuali che si sta tentando di crittografare siano comprese tra le [macchine virtuali con dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems) e che siano stati soddisfatti tutti i prerequisiti:

- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](disk-encryption-overview.md#group-policy-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall

Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie. È quindi possibile che vengano visualizzati messaggi del tipo "Stato estensione non disponibile nella macchina virtuale". In scenari previsti, la crittografia non viene completata. Le sezioni che seguono illustrano alcuni problemi comuni relativi ai firewall che è possibile esaminare.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i [prerequisiti](disk-encryption-overview.md#networking-requirements) di configurazione di rete documentati per la crittografia dei dischi.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault protetto da firewall

Quando la crittografia è abilitata con le [credenziali di Azure AD](disk-encryption-windows-aad.md#), la macchina virtuale di destinazione deve consentire la connettività sia sugli endpoint di Azure Active Directory sia sugli endpoint di Key Vault. Gli endpoint di autenticazione Azure Active Directory correnti sono conservati nelle sezioni 56 e 59 della documentazione relativa agli [url Microsoft 365 e agli intervalli di indirizzi IP](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Le istruzioni di Key Vault sono fornite nella documentazione [Accedere ad Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure 
La macchina virtuale deve essere in grado di accedere all'endpoint del [servizio metadati dell'istanza di Azure](../windows/instance-metadata-service.md) che usa un indirizzo IP noto e non instradabile (`169.254.169.254`) accessibile solo dall'interno della macchina virtuale.  Non sono supportate le configurazioni proxy che modificano il traffico HTTP locale a questo indirizzo, ad esempio aggiungendo un'intestazione X-Forwarded-For.

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

È possibile che nel portale un disco venga visualizzato come crittografato anche dopo essere stato decrittografato nella macchina virtuale.  Questo problema può verificarsi quando si usano comandi di basso livello per decrittografare il disco direttamente dalla macchina virtuale, anziché usare i comandi di gestione di livello superiore di Crittografia dischi di Azure.  I comandi di livello superiore, infatti, non solo consentono di decrittografare il disco direttamente dalla macchina virtuale ma, all'esterno della macchina virtuale, consentono anche di aggiornare al livello di piattaforma le impostazioni di estensione e di crittografia associate alla macchina virtuale.  Se le impostazioni non sono allineate, la piattaforma non sarà in grado di segnalare correttamente lo stato della crittografia o di effettuare il provisioning della macchina virtuale.

Per disabilitare Crittografia dischi di Azure con PowerShell, usare il comando [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguito da [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). L'esecuzione del comando Remove-AzVMDiskEncryptionExtension prima di disabilitare la crittografia avrà, infatti, esito negativo.

Per disabilitare Crittografia dischi di Azure con l'interfaccia della riga di comando, usare [az vm encryption disable](/cli/azure/vm/encryption). 

## 



## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere gli articoli seguenti:

- [Applicare la crittografia dei dischi nel Centro sicurezza di Azure](../../security-center/security-center-virtual-machine-protection.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
