---
title: Che cos'è crittografia dischi di Azure?
description: Questo articolo offre una panoramica di crittografia dischi di Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b00a5262b1e144aa47cd7fd640906225ff4fecd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068796"
---
# <a name="azure-disk-encryption-overview"></a>Panoramica di crittografia dischi di Azure

Crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Usa il [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funzionalità di Windows e il [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funzionalità di Linux per fornire la crittografia del volume per i dischi del sistema operativo e dati delle macchine virtuali di Azure (VM). È anche integrato con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) che consentono di controllare e gestire le chiavi di crittografia dei dischi e i segreti e assicura che tutti i dati nei dischi della VM vengono crittografati quando inattivi in archiviazione di Azure. Crittografia del disco per Windows e macchine virtuali Linux di Azure è a livello generale in tutte le aree pubbliche di Azure e le aree di Azure per enti pubblici per macchine virtuali Standard e macchine virtuali con archiviazione Premium di Azure. 

Se si usa Centro sicurezza di Azure, si viene avvisati se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.


## <a name="encryption-scenarios"></a>Scenari di crittografia

Con crittografia dischi di Azure, è possibile indirizzare i requisiti di conformità e sicurezza dell'organizzazione attraverso la protezione di macchine virtuali di Azure a riposo usando la tecnologia di crittografia standard del settore. È anche possibile configurare le macchine virtuali per l'avvio in criteri (BYOK) e le chiavi controllate dai clienti e controllare l'utilizzo di queste chiavi nell'insieme di credenziali delle chiavi.

Crittografia dischi di Azure supporta gli scenari cliente seguenti:

* L'abilitazione e disabilitazione della crittografia nelle nuove VM create da immagini della raccolta di Azure supportate.
* L'abilitazione e disabilitazione della crittografia nelle macchine virtuali esistenti eseguiti in Azure.
* L'abilitazione e disabilitazione della crittografia nelle nuove macchine virtuali Windows create da chiavi di crittografia e VHD pre-crittografato.
* Abilitazione e disabilitazione della crittografia in scalabilità di macchine virtuali di Windows imposta.
* Abilitazione e disabilitazione della crittografia in data unità per i set di scalabilità di macchine virtuali Linux.
* L'abilitazione e disabilitazione della crittografia della VM con managed Disks.
* Aggiornamento delle impostazioni di crittografia di archiviazione VM non Premium e Premium crittografata esistente.
* Backup e il ripristino di macchine virtuali crittografate.
* Portare il proprio crittografia (BYOE) e portare i propri scenari chiave (BYOK), in cui i clienti, usano le proprie chiavi di crittografia e averle archiviano in Azure key vault.

Supporta anche gli scenari seguenti per le macchine virtuali quando sono abilitate in Microsoft Azure:

* Integrazione con Azure Key Vault.
* [Macchine virtuali di livello standard](https://azure.microsoft.com/pricing/details/virtual-machines/). Le [macchine virtuali Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) all'interno di questi livelli devono soddisfare il requisito di memoria minimo di 7 GB. 
* Abilitazione della crittografia in Windows e macchine virtuali Linux, dischi gestiti e di scalabilità macchine virtuali del set di immagini della raccolta di Azure supportate.
* La disabilitazione della crittografia nel sistema operativo e dati unità per le macchine virtuali Windows, di scalabilità macchine virtuali del set e macchine virtuali con dischi gestiti.
* La disabilitazione della crittografia nelle unità dati per le macchine virtuali Linux, scalabilità macchine virtuali del set e macchine virtuali con dischi gestiti.
* Abilitazione della crittografia nelle macchine virtuali che eseguono il sistema operativo Client Windows.
* Abilitazione della crittografia su volumi con percorsi di montaggio.
* Abilitazione della crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID tramite mdadm).
* Abilitazione della crittografia nelle macchine virtuali Linux usare LVM per i dischi dati.
* Abilitazione della crittografia dei dischi del sistema operativo della macchina virtuale Linux e i dati.

   > [!NOTE]
   > La crittografia dell'unità del sistema operativo per alcune distribuzioni Linux non è supportata. Per altre informazioni, vedere l'articolo [Domande frequenti su Crittografia dischi di Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
   
* Abilitazione della crittografia nelle macchine virtuali configurate con inizio spazi di archiviazione Windows in Windows Server 2016.
* Eseguire il backup e ripristino di macchine virtuali crittografate per chiave di crittografia della chiave (KEK) e gli scenari non KEK.

Crittografia dischi di Azure non funziona per gli scenari le seguenti funzionalità, la tecnologia:

* La crittografia di livello basic della macchina virtuale o le macchine virtuali create tramite il metodo di creazione della macchina virtuale classica.
* Disabilitazione della crittografia in un'unità del sistema operativo o un'unità di dati di una VM Linux quando il disco del sistema operativo è crittografato.
* Imposta la crittografia delle unità del sistema operativo per la scalabilità di macchine virtuali Linux.
* Crittografia delle macchine virtuali Windows configurate con sistemi RAID basati su software.
* Crittografia delle immagini personalizzate nelle macchine virtuali Linux.
* Integrazione con un sistema di gestione delle chiavi in locale.
* File di Azure (file system condiviso).
* NFS (Network File System).
* Volumi dinamici.

## <a name="encryption-features"></a>Funzionalità di crittografia

Quando si abilita e si distribuisce la crittografia dischi di Azure per macchine virtuali di Azure, è possibile configurare le seguenti funzionalità da abilitare:

* Crittografare il volume del sistema operativo per proteggere il volume di avvio quando sono inattivi nella risorsa di archiviazione.
* La crittografia dei volumi di dati per proteggere i volumi dati inattivi nella risorsa di archiviazione.
* La disabilitazione della crittografia nelle unità del sistema operativo e i dati per le macchine virtuali Windows.
* La disabilitazione della crittografia dei dati le unità per le macchine virtuali Linux (solo quando l'unità del sistema operativo non è crittografato).
* Protezione delle chiavi di crittografia e i segreti nella sottoscrizione di Azure Key Vault.
* Segnalazione dello stato di crittografia della macchina virtuale crittografata.
* Rimuovere le impostazioni di configurazione di crittografia del disco dalla macchina virtuale.
* Backup e il ripristino di macchine virtuali crittografate tramite il servizio Backup di Azure.

Azure Disk Encryption per le macchine virtuali per Windows e Linux include:

* [L'estensione di crittografia del disco per Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [L'estensione di crittografia del disco per Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [I cmdlet di crittografia dischi di PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [I cmdlet di crittografia del disco della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [I modelli di crittografia dischi di Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

Crittografia dischi di Azure è supportata nelle macchine virtuali che eseguono Windows o del sistema operativo Linux. Per altre informazioni sui sistemi operativi supportati, vedere [domande frequenti](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).

> [!NOTE]
> Non è previsto alcun addebito aggiuntivo per crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure. All'insieme di credenziali delle chiavi usato per archiviare le chiavi di crittografia si applicano i [prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) Standard. 

## <a name="encryption-workflow"></a>Flusso di lavoro della crittografia

Per abilitare la crittografia del disco per macchine virtuali Windows e Linux, eseguire i passaggi seguenti:

1. Acconsentire esplicitamente ad abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale.
   * Per nuove macchine virtuali che vengono create da immagini della raccolta supportati e le macchine virtuali esistenti già eseguiti in Azure, fornire la configurazione della crittografia per abilitare la crittografia nella macchina virtuale.

1. Concedere l'accesso alla piattaforma Azure per leggere il materiale della chiave di crittografia (chiavi di crittografia BitLocker per i sistemi Windows) e la Passphrase per Linux da insieme di credenziali delle chiavi per abilitare la crittografia nella macchina virtuale.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.

   ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Flusso di lavoro della decrittografia
Per disabilitare la crittografia del disco per le macchine virtuali, completare i passaggi generali seguenti:

1. Scegliere di disabilitare la crittografia (decrittografia) in una VM in esecuzione in Azure e specificare la configurazione della decrittografia. È possibile disabilitare la crittografia tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure.

   Questo passaggio disabilita la crittografia del sistema operativo o il volume di dati o entrambi nella VM Windows in esecuzione. Come indicato nella sezione precedente la disabilitazione della crittografia del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux, purché il disco del sistema operativo non sia crittografato.

1. Azure aggiorna il modello di servizio della macchina virtuale e la macchina virtuale è contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

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

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale.
   * Per le macchine virtuali esistenti già eseguiti in Azure e le nuove macchine virtuali create dal Marketplace, fornire la configurazione della crittografia per abilitare la crittografia nella macchina virtuale.

1. Concedere l'accesso alla piattaforma Azure per leggere il materiale della chiave di crittografia (chiavi di crittografia BitLocker per i sistemi Windows) e la Passphrase per Linux da insieme di credenziali delle chiavi per abilitare la crittografia nella macchina virtuale.

1. Specificare l'identità dell'applicazione di Azure AD per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Questo passaggio abilita la crittografia nella VM per gli scenari indicati nel passaggio 2.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.


## <a name="terminology"></a>Terminologia
La tabella seguente illustra alcuni dei termini comuni usati nella documentazione di crittografia dischi di Azure:

| Terminologia | Definizione |
| --- | --- |
| Azure AD | Un account [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) è usato per autenticare, archiviare e recuperare i segreti da un insieme di credenziali delle chiavi. |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una settoriali Windows volume tecnologia di crittografia utilizzato per abilitare la crittografia del disco nelle macchine virtuali di Windows. |
| BEK | Le chiavi di crittografia BitLocker (BEK, Bitlocker Encryption Key) vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BEK sono protette nell'insieme di credenziali delle chiavi come segreti. |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) è il sottosistema di crittografia del disco trasparente, basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali Linux. |
| Chiave di crittografia della chiave (KEK) | La chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere la [prerequisiti di crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md).

