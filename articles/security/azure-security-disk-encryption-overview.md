---
title: Che cos'è crittografia dischi di Azure?
description: Questo articolo fornisce una panoramica di crittografia dischi di Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfcc69d90daae5869c3b69c922e99eab3585e14
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164629"
---
# <a name="azure-disk-encryption-overview"></a>Panoramica di crittografia dischi di Azure

Crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Usa la funzionalità [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure. È inoltre integrato con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografia del disco e garantisce che tutti i dati nei dischi delle macchine virtuali siano crittografati durante l'archiviazione di Azure. Crittografia dischi di Azure per macchine virtuali Windows e Linux è disponibile a livello generale in tutte le aree pubbliche di Azure e nelle aree di Azure per enti pubblici per macchine virtuali e VM standard con archiviazione Premium di Azure. 

Se si usa Centro sicurezza di Azure, si viene avvisati se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.


## <a name="encryption-scenarios"></a>Scenari di crittografia

Con crittografia dischi di Azure è possibile soddisfare i requisiti di sicurezza e conformità dell'organizzazione proteggendo le macchine virtuali di Azure a riposo usando la tecnologia di crittografia standard del settore. È anche possibile configurare le macchine virtuali per l'avvio con chiavi e criteri controllati dal cliente (BYOK) e controllare l'utilizzo di queste chiavi nell'insieme di credenziali delle chiavi.

Crittografia dischi di Azure supporta i seguenti scenari di clienti:

* Abilitazione e disabilitazione della crittografia nelle nuove macchine virtuali create dalle immagini della raccolta di Azure supportate.
* Abilitazione e disabilitazione della crittografia nelle macchine virtuali esistenti in esecuzione in Azure.
* Abilitazione e disabilitazione della crittografia in nuove macchine virtuali Windows create da chiavi di crittografia e VHD pre-crittografate.
* Abilitazione e disabilitazione della crittografia nei set di scalabilità di macchine virtuali Windows.
* Abilitazione e disabilitazione della crittografia nelle unità dati per i set di scalabilità di macchine virtuali Linux.
* Abilitazione e disabilitazione della crittografia delle macchine virtuali con dischi gestiti.
* Aggiornamento delle impostazioni di crittografia di una macchina virtuale di archiviazione Premium e non Premium crittografata esistente.
* Backup e ripristino di macchine virtuali crittografate.
* Bring your own Encryption (BYOE) e scenari BYOK (Bring your own key), in cui i clienti usano le proprie chiavi di crittografia e le archiviano in un insieme di credenziali delle chiavi di Azure.

Supporta anche gli scenari seguenti per le macchine virtuali quando sono abilitate in Microsoft Azure:

* Integrazione con Azure Key Vault.
* [VM di livello standard](https://azure.microsoft.com/pricing/details/virtual-machines/) che soddisfano i [requisiti di memoria minimi](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Abilitazione della crittografia nelle VM Windows e Linux, nel disco gestito e nei set di scalabilità dalle immagini della raccolta di Azure supportate.
* Disabilitare la crittografia nel sistema operativo e nelle unità dati per le macchine virtuali Windows, le macchine virtuali dei set di scalabilità e le VM con dischi gestiti.
* Disabilitare la crittografia nelle unità dati per le macchine virtuali Linux, le macchine virtuali del set di scalabilità e le macchine virtuali con dischi gestiti.
* Abilitazione della crittografia nelle macchine virtuali che eseguono il sistema operativo client Windows.
* Abilitazione della crittografia su volumi con percorsi di montaggio.
* Abilitazione della crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite mdadm.
* Abilitazione della crittografia nelle macchine virtuali Linux che usano LVM per i dischi dati.
* Abilitazione della crittografia nel sistema operativo della macchina virtuale Linux e nei dischi dati.

   > [!NOTE]
   > La crittografia dell'unità del sistema operativo per alcune distribuzioni Linux non è supportata. Per ulteriori informazioni, vedere [sistemi operativi supportati da crittografia dischi di Azure: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Abilitazione della crittografia nelle macchine virtuali configurate con gli spazi di archiviazione di Windows a partire da Windows Server 2016. Spazi di archiviazione diretta (S2D) non è ancora supportata.
* Eseguire il backup e il ripristino di macchine virtuali crittografate sia per gli scenari di chiave di crittografia chiave (KEK) che per quelli non KEK.

Crittografia dischi di Azure non funziona per gli scenari, le funzionalità e le tecnologie seguenti:

* Crittografia di VM di livello Basic o macchine virtuali create tramite il metodo di creazione della macchina virtuale classica.
* Disabilitare la crittografia in un'unità del sistema operativo o in un'unità dati di una VM Linux quando l'unità del sistema operativo è crittografata.
* Crittografia dell'unità del sistema operativo per i set di scalabilità di macchine virtuali Linux.
* Crittografia delle macchine virtuali Windows configurate con sistemi RAID basati su software.
* Crittografia di immagini personalizzate in macchine virtuali Linux.
* Integrazione con un sistema di gestione delle chiavi locale.
* File di Azure (file system condiviso).
* NFS (Network File System).
* Volumi dinamici.
* Dischi del sistema operativo effimeri.
* Crittografia dei file system condivisi/distribuiti, ad esempio (ma non limitati): DFS, GFS, DRDB, CephFS e così via

## <a name="encryption-features"></a>Funzionalità di crittografia

Quando si Abilita e si distribuisce crittografia dischi di Azure per le macchine virtuali di Azure, è possibile configurare le seguenti funzionalità da abilitare:

* Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nell'archiviazione.
* Crittografia dei volumi di dati per proteggere i volumi di dati inattivi nell'archiviazione.
* Disabilitare la crittografia nel sistema operativo e nelle unità dati per le macchine virtuali Windows.
* Disabilitazione della crittografia nelle unità dati per le macchine virtuali Linux (solo quando l'unità del sistema operativo non è crittografata).
* Protezione delle chiavi di crittografia e dei segreti nella sottoscrizione Azure Key Vault.
* Segnalazione dello stato di crittografia della VM crittografata.
* Rimozione delle impostazioni di configurazione della crittografia del disco dalla macchina virtuale.
* Backup e ripristino delle macchine virtuali crittografate con il servizio backup di Azure.

Crittografia dischi di Azure per le macchine virtuali per Windows e Linux include:

* [Estensione di crittografia del disco per Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Estensione di crittografia del disco per Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Cmdlet di crittografia del disco di PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Cmdlet di crittografia del disco dell'interfaccia della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [I modelli di crittografia del disco Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Non è previsto alcun addebito aggiuntivo per crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure. All'insieme di credenziali delle chiavi usato per archiviare le chiavi di crittografia si applicano i [prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) Standard. 

## <a name="encryption-workflow"></a>Flusso di lavoro della crittografia

Per abilitare la crittografia del disco per macchine virtuali Windows e Linux, eseguire i passaggi seguenti:

1. Acconsentire esplicitamente ad abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Fornire quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale.
   * Per le nuove macchine virtuali create da immagini della raccolta supportate e macchine virtuali esistenti già in esecuzione in Azure, fornire la configurazione della crittografia per abilitare la crittografia nella macchina virtuale.

1. Concedere l'accesso alla piattaforma Azure per leggere il materiale della chiave di crittografia (chiavi di crittografia BitLocker per i sistemi Windows e passphrase per Linux) dall'insieme di credenziali delle chiavi per abilitare la crittografia nella macchina virtuale.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.

   ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Flusso di lavoro della decrittografia
Per disabilitare la crittografia del disco per le VM, completare i passaggi di alto livello seguenti:

1. Scegliere di disabilitare la crittografia (decrittografia) in una macchina virtuale in esecuzione in Azure e specificare la configurazione di decrittografia. È possibile disabilitare la crittografia tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure.

   Questo passaggio Disabilita la crittografia del sistema operativo o del volume di dati o entrambi nella VM Windows in esecuzione. Come indicato nella sezione precedente la disabilitazione della crittografia del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux, purché il disco del sistema operativo non sia crittografato.

1. Azure aggiorna il modello del servizio VM e la macchina virtuale viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

   > [!NOTE]
   > L'operazione di disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per sistemi Windows o Passphrase per Linux.
   >
   > La disabilitazione della crittografica del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux.
   >
   > La disabilitazione della crittografia del disco dati per Linux non è supportata se l'unità del sistema operativo è crittografata.


## <a name="encryption-workflow-previous-release"></a>Flusso di lavoro di crittografia (versione precedente)

La nuova versione di Crittografia dischi di Azure elimina la necessità di specificare un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia dei dischi per le macchine virtuali. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD quando si usa la nuova versione. Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di Azure AD. Per abilitare la crittografia dei dischi per macchine virtuali Windows e Linux (versione precedente), seguire questa procedura:

1. Scegliere uno scenario di crittografia tra quelli elencati nella sezione [Scenari di crittografia](#encryption-scenarios).

1. Acconsentire esplicitamente ad abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Fornire quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale.
   * Per le nuove macchine virtuali create dal Marketplace e macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella macchina virtuale.

1. Concedere l'accesso alla piattaforma Azure per leggere il materiale della chiave di crittografia (chiavi di crittografia BitLocker per i sistemi Windows e passphrase per Linux) dall'insieme di credenziali delle chiavi per abilitare la crittografia nella macchina virtuale.

1. Specificare l'identità dell'applicazione di Azure AD per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Questo passaggio consente la crittografia nella macchina virtuale per gli scenari indicati nel passaggio 2.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.


## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione di crittografia dischi di Azure:

| Terminologia | Definizione |
| --- | --- |
| Azure AD | Un account [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) è usato per autenticare, archiviare e recuperare i segreti da un insieme di credenziali delle chiavi. |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia dei dischi nelle macchine virtuali Windows. |
| BEK | Le chiavi di crittografia BitLocker (BEK, Bitlocker Encryption Key) vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BEK sono protette nell'insieme di credenziali delle chiavi come segreti. |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) è il sottosistema di crittografia dei dischi trasparente basato su Linux usato per abilitare la crittografia dei dischi nelle macchine virtuali Linux. |
| Chiave di crittografia della chiave (KEK) | Chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere i [prerequisiti di crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md).

