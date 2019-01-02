---
title: Panoramica - Crittografia dischi di Azure per macchine virtuali IaaS | Microsoft Docs
description: Questo articolo offre una panoramica della Crittografia dischi di Microsoft Azure per macchine virtuali IaaS.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 73a00756928fd476b723e0b43accf46378ae14cc
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093285"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Crittografia dischi di Azure per macchine virtuali IaaS

Microsoft Azure si impegna per assicurare la privacy e la sovranità dei dati dell'utente. Azure consente di controllare i dati ospitati da Azure tramite diverse tecnologie avanzate per crittografare, controllare e gestire le chiavi di crittografia e controllare l'accesso dei dati. I clienti di Azure hanno quindi la possibilità di scegliere la soluzione che meglio soddisfa le proprie esigenze aziendali. Questo articolo presenta una soluzione tecnologica: "Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux". Questa tecnologia consente di proteggere i dati per soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Panoramica

Crittografia dischi di Azure è una funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi sfrutta la funzionalità standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Crittografia dischi per macchine virtuali IaaS Windows e Linux ha disponibilità generale in tutte le aree pubbliche e nelle aree di Azure per enti pubblici per macchine virtuali standard e macchine virtuali con Archiviazione Premium di Azure. Quando si applica la soluzione di gestione Crittografia dischi, è possibile soddisfare le esigenze aziendali seguenti:

* Le macchine virtuali IaaS inattive sono protette con la tecnologia di crittografia standard, per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.
* Le macchine virtuali IaaS vengono avviate con chiavi e criteri controllati dai clienti. È possibile controllarne l'utilizzo nell'insieme di credenziali delle chiavi.

Se si usa Centro sicurezza di Azure, si viene avvisati se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.


## <a name="encryption-scenarios"></a>Scenari di crittografia

La soluzione Crittografia dischi supporta gli scenari dei clienti descritti di seguito:

* Abilitare la crittografia nelle nuove macchine virtuali IaaS Windows create da chiavi di crittografia e dischi rigidi virtuali pre-crittografati.
* Abilitare la crittografia in nuove macchine virtuali IaaS create da immagini della raccolta di Azure supportate.
* Abilitare la crittografia in macchine virtuali IaaS esistenti in esecuzione in Azure.
* Abilitare la crittografia nei set di scalabilità di macchine virtuali Windows.
* Abilitare la crittografia nelle unità dati per i set di scalabilità di macchine virtuali Linux.
* Disabilitare la crittografia nelle macchine virtuali IaaS Windows.
* Disabilitare la crittografia nelle unità dati per le macchine virtuali IaaS Linux.
* Disabilitare la crittografia nei set di scalabilità di macchine virtuali Windows.
* Disabilitare la crittografia nelle unità dati per i set di scalabilità di macchine virtuali Linux.
* Abilitare la crittografia delle macchine virtuali con disco gestito.
* Aggiornare le impostazioni di crittografia di una macchina virtuale dotata di archiviazione Premium e non Premium crittografata esistente.
* Eseguire il backup e il ripristino di macchine virtuali crittografate.

La soluzione supporta gli scenari seguenti per le macchine virtuali IaaS, se abilitati in Microsoft Azure:

* Integrazione con Azure Key Vault.
* Macchine virtuali di livello Standard: [serie A, D, DS, G, GS, F e così via per macchine virtuali IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/). Le [macchine virtuali Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) all'interno di questi livelli devono soddisfare il requisito di memoria minimo di 7 GB.
* Abilitare la crittografia nelle macchine virtuali IaaS Windows e Linux, nelle macchine virtuali con disco gestito e nelle macchine virtuali del set di scalabilità dalle immagini della raccolta di Azure supportate.
* Disabilitare la crittografia nel sistema operativo e nelle unità dati per le macchine virtuali IaaS Windows, per le macchine virtuali del set di scalabilità e per le macchine virtuali con disco gestito.
* Disabilitare la crittografia nelle unità dati per le macchine virtuali IaaS Linux, per le macchine virtuali del set di scalabilità e per le macchine virtuali con disco gestito.
* Abilitare la crittografia in macchine virtuali IaaS eseguite nel sistema operativo client Windows.
* Abilitare la crittografia su volumi con percorsi di montaggio.
* Abilitare la crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite mdadm.
* Abilitare la crittografia nelle macchine virtuali Linux che usano LVM per i dischi dati.
* Abilitare la crittografia nelle macchine virtuali Linux per i dischi del sistema operativo e i dischi dati.

   > [!NOTE]
   > La crittografia dell'unità del sistema operativo per alcune distribuzioni Linux non è supportata. Per altre informazioni, vedere l'articolo [Domande frequenti su Crittografia dischi di Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
   
* Abilitare la crittografia nelle macchine virtuali di Windows configurate usando spazi di archiviazione Windows.
* Aggiornare le impostazioni di crittografia per una macchina virtuale dotata di archiviazione Premium e non Premium crittografata esistente.
* Eseguire il backup e il ripristino di macchine virtuali crittografate per scenari con e senza KEK (Key Encryption Key).
* Sono supportate tutte le aree di Azure pubbliche e di Azure per enti pubblici.

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti:

* Macchine virtuali IaaS del piano Basic.
* Disabilitare la crittografia in un'unità del sistema operativo per le macchine virtuali IaaS Linux.
* Disabilitare la crittografia in un'unità di dati quando l'unità del sistema operativo è crittografata per le macchine virtuali Iaas di Linux.
* Macchine virtuali IaaS create usando il metodo di creazione classico per le macchine virtuali.
* Abilitare la crittografia delle immagini personalizzate nelle macchine virtuali IaaS Linux.
* Integrazione con il sistema di gestione delle chiavi locale.
* File di Azure (file system condiviso).
* NFS (Network File System).
* Volumi dinamici.
* Macchine virtuali Windows configurate con sistemi RAID basati su software.

## <a name="encryption-features"></a>Funzionalità di crittografia

Quando si abilita e si distribuisce la Crittografia dischi per le macchine virtuali IaaS di Azure, sono abilitate le funzionalità seguenti, a seconda della configurazione fornita:

* Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nella risorsa di archiviazione dell'utente.
* Crittografia dei volumi dati per proteggere i volumi dati inattivi nella risorsa di archiviazione dell'utente.
* Disabilitare la crittografia e nelle unità dati e del sistema operativo per le macchine virtuali IaaS Windows.
* Disabilitare la crittografia nelle unità dati per le macchine virtuali IaaS Linux (solo quando l'unità del sistema operativo non è crittografata).
* Proteggere le chiavi di crittografia e i segreti nella sottoscrizione di Azure Key Vault.
* Segnalare lo stato di crittografia della macchina virtuale IaaS crittografata.
* Rimuovere le impostazioni di configurazione della crittografia del disco dalla macchina virtuale IaaS.
* Eseguire un backup e ripristino delle macchine virtuali crittografate usando il servizio Backup di Azure.

Crittografia dischi di Azure per macchine virtuali IaaS per soluzioni Windows e Linux include:

* Estensione della crittografia dischi per Windows.
* Estensione della crittografia dischi per Linux.
* Cmdlet di crittografia del disco di PowerShell.
* Cmdlet di crittografia del disco dell'interfaccia della riga di comando di Azure.
* Modelli di crittografia del disco di Azure Resource Manager.

La soluzione Crittografia dischi di Azure è supportata nelle macchine virtuali IaaS che eseguono il sistema operativo Windows o Linux. Per altre informazioni sui sistemi operativi supportati, vedere l'articolo [Prerequisiti](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Non è previsto alcun addebito aggiuntivo per crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure. All'insieme di credenziali delle chiavi usato per archiviare le chiavi di crittografia si applicano i [prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) Standard. 


## <a name="encryption-workflow"></a>Flusso di lavoro della crittografia

Per abilitare la crittografia del disco per macchine virtuali Windows e Linux, eseguire i passaggi seguenti:

1. Scegliere uno scenario di crittografia tra quelli elencati nella sezione [Scenari di crittografia](#encryption-scenarios).

1. Acconsentire esplicitamente ad abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale IaaS.
   * Per le nuove macchine virtuali create dal Marketplace e per le macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella macchina virtuale IaaS.

1. Concedere l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella macchina virtuale IaaS.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.

   ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Flusso di lavoro della decrittografia
Per disabilitare la crittografia dei dischi per le macchine virtuali IaaS, seguire questa procedura generale:

1. Scegliere di disabilitare la crittografia (decrittografia) in una macchina virtuale IaaS in esecuzione in Azure e specificare la configurazione della decrittografia. È possibile disabilitare la crittografia tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure.

   Questo passaggio agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Come indicato nella sezione precedente la disabilitazione della crittografia del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux, purché il disco del sistema operativo non sia crittografato.

1. Azure aggiorna il modello di servizi della macchina virtuale e la macchina virtuale IaaS viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

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

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale IaaS.
   * Per le nuove macchine virtuali create dal Marketplace e per le macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella macchina virtuale IaaS.

1. Concedere l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella macchina virtuale IaaS.

1. Specificare l'identità dell'applicazione di Azure AD per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Questo passaggio abilita la crittografia nella macchina virtuale IaaS per gli scenari indicati nel passaggio 2.

1. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.


## <a name="terminology"></a>Terminologia
La tabella seguente illustra alcuni dei termini comuni usati in questa tecnologia:

| Terminologia | Definizione |
| --- | --- |
| Azure AD | Un account [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) è usato per autenticare, archiviare e recuperare i segreti da un insieme di credenziali delle chiavi. |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia dei dischi nelle macchine virtuali IaaS di Windows. |
| BEK | Le chiavi di crittografia BitLocker (BEK, Bitlocker Encryption Key) vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BEK sono protette nell'insieme di credenziali delle chiavi come segreti. |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali IaaS Linux. |
| KEK | La chiave di crittografia della chiave (KEK, Key Encryption Key) è la chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o per eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Prerequisiti di Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md)
