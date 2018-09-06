---
title: Panoramica della crittografia dischi di Azure per macchine virtuali IaaS | Microsoft Docs
description: Questo articolo offre una panoramica della Crittografia dischi di Microsoft Azure per macchine virtuali IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: 88500be4bae83049e8a7060719f4f85e7622c645
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886992"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Crittografia dischi di Azure per macchine virtuali IaaS 
Microsoft Azure è caratterizzato dal massimo impegno volto ad assicurare la privacy e la sovranità dei dati e a consentire il controllo dei dati ospitati in Azure con una gamma di tecnologie avanzate per crittografare, controllare e gestire le chiavi di crittografia e controllare e verificare l'accesso ai dati. I clienti di Azure hanno quindi la possibilità di scegliere la soluzione che meglio soddisfa le proprie esigenze aziendali. In questo articolo viene introdotta una soluzione tecnologica, "Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux", che facilita la protezione e la salvaguardia dei dati per rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Panoramica
Crittografia dischi di Azure è una funzionalità che consente di crittografare i dischi di macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure applica la funzionalità standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per offrire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux ha **disponibilità generale** in tutte le aree pubbliche e AzureGov di Azure per macchine virtuali standard e macchine virtuali con archiviazione Premium. Quando si applica la soluzione di gestione Crittografia dischi di Azure, è possibile soddisfare le esigenze aziendali seguenti:

* Le macchine virtuali IaaS inattive sono protette con la tecnologia di crittografia standard, per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.
* Le macchine virtuali IaaS vengono avviate con chiavi e criteri controllati dai clienti ed è possibile controllarne l'utilizzo nell'insieme di credenziali delle chiavi.


Se si usa Centro sicurezza di Azure, il servizio invia un avviso se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.
![Avviso crittografia dischi in Centro sicurezza di Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.


## <a name="encryption-scenarios"></a>Scenari di crittografia
La soluzione Crittografia dischi di Azure supporta i tre scenari dei clienti descritti di seguito:

* Abilitare la crittografia nelle nuove macchine virtuali IaaS Windows create da chiavi di crittografia e dischi rigidi virtuali pre-crittografati 
* Abilitare la crittografia in nuove VM IaaS create da immagini della raccolta di Azure supportate
* Abilitare la crittografia in VM IaaS esistenti in esecuzione in Azure
* Abilitare la crittografia nei set di scalabilità di macchine virtuali Windows
* Abilitare la crittografia nelle unità dati per i set di scalabilità di macchine virtuali Linux
* Disabilitare la crittografia nelle VM IaaS Windows
* Disabilitare la crittografia nelle unità dati per le VM IaaS Linux
* Disabilitare la crittografia nei set di scalabilità di macchine virtuali Windows
* Disabilitare la crittografia nelle unità dati per i set di scalabilità di macchine virtuali Linux
* Abilitare la crittografia delle macchine virtuali con disco gestito
* Aggiornare le impostazioni di crittografia di una macchina virtuale dotata di archiviazione Premium e non Premium crittografata esistente
* Eseguire il backup e il ripristino di macchine virtuali crittografate

La soluzione supporta gli scenari seguenti per le macchine virtuali IaaS, se abilitati in Microsoft Azure:

* Integrazione dell'insieme di credenziali delle chiavi di Azure.
* Macchine virtuali di livello Standard: [serie A, D, DS, G, GS, F e così via per VM IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * [Le macchine virtuali Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) all'interno di questi livelli devono soddisfare il requisito di memoria minimo di 7 GB
* Abilitare la crittografia nelle macchine virtuali IaaS Windows e Linux, nelle macchine virtuali con disco gestito e nelle macchine virtuali del set di scalabilità dalle immagini della raccolta di Azure supportate
* Disabilitare la crittografia nel sistema operativo e nelle unità dati per le macchine virtuali IaaS Windows, per le macchine virtuali del set di scalabilità e per le macchine virtuali con disco gestito
* Disabilitare la crittografia nelle unità dati per le macchine virtuali IaaS Linux, per le macchine virtuali del set di scalabilità e per le macchine virtuali con disco gestito
* Abilitare la crittografia in VM IaaS eseguite nel sistema operativo client Windows
* Abilitare la crittografia su volumi con percorsi di montaggio
* Abilitare la crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite mdadm
* Abilitare la crittografia nelle macchine virtuali Linux usando LVM per i dischi dati
* Abilitare la crittografia in macchine virtuali Linux per i dischi del sistema operativo e i dischi dati 
* Abilitare la crittografia nelle VM Windows configurate con spazi di archiviazione
* Aggiornare le impostazioni di crittografia di una macchina virtuale dotata di archiviazione Premium e non Premium crittografata esistente
* Eseguire il backup e il ripristino di macchine virtuali crittografate, per scenari con e senza KEK (Key Encryption Key)
* Sono supportate tutte le aree di Azure pubbliche e AzureGov

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti:

* VM IaaS del piano Basic
* Disabilitazione della crittografia in un'unità del sistema operativo per le VM IaaS Linux
* Disabilitazione della crittografia in un'unità di dati se l'unità del sistema operativo è crittografata per le macchine virtuali Iaas di Linux
* Macchine virtuali IaaS create usando il metodo di creazione classico per le macchine virtuali
* Abilitazione della crittografia per le immagini personalizzate nelle macchine virtuali IaaS Linux
* Integrazione con il servizio di gestione delle chiavi locale.
* File di Azure (file system condiviso), file system di rete (NFS, Network File System), volumi dinamici e macchine virtuali Windows configurate con sistemi RAID basati su software

## <a name="encryption-features"></a>Funzionalità di crittografia
Quando si abilita e si distribuisce la Crittografia dischi di Azure per le VM IaaS di Azure, sono abilitate le funzionalità seguenti, a seconda della configurazione fornita:

* Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nella risorsa di archiviazione dell'utente
* Crittografia dei volumi dati per proteggere i volumi dati inattivi nella risorsa di archiviazione dell'utente
* Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows
* Disabilitazione della crittografia nelle unità dati per le macchine virtuali IaaS Linux (solo se l'unità del sistema operativo non è crittografata)
* Protezione delle chiavi di crittografia e dei segreti nella sottoscrizione di Key Vault
* Segnalazione dello stato di crittografia della macchina virtuale IaaS crittografata
* Rimozione delle impostazioni di configurazione della crittografia del disco dalla macchina virtuale IaaS
* Backup e ripristino delle macchine virtuali crittografate usando il servizio Backup di Azure

Crittografia dischi di Azure per macchine virtuali IaaS per soluzioni Windows e Linux include:

* Estensione della crittografia dischi per Windows.
* Estensione della crittografia dischi per Linux.
* Cmdlet di PowerShell per la crittografia del disco.
* Cmdlet dell'interfaccia della riga di comando di Azure per la crittografia del disco.
* Modelli di Azure Resource Manager per la crittografia del disco.

La soluzione Crittografia dischi di Azure è supportata nelle macchine virtuali IaaS in esecuzione in sistemi operativi Windows o Linux. Per altre informazioni sui sistemi operativi supportati, vedere l'articolo [Prerequisiti](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Non è previsto alcun addebito aggiuntivo per la crittografia dei dischi delle macchine virtuali con Crittografia dischi di Azure. All'insieme di credenziali delle chiavi usato per archiviare le chiavi di crittografia si applicano i [prezzi del Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) standard. 


## <a name="encryption-workflow"></a>Flusso di lavoro della crittografia

 Per abilitare la crittografia del disco per macchine virtuali Windows e Linux, eseguire i passaggi seguenti:

1. Scegliere uno scenario di crittografia tra gli scenari di crittografia precedenti.
2. Acconsentire esplicitamente per abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o un comando dell'interfaccia della riga di comando e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale IaaS.
   * Per le nuove macchine virtuali create dal Marketplace e per le macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella VM IaaS.

3. Concedere l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS.

4. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la macchina virtuale crittografata.

 ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Flusso di lavoro della decrittografia
Per disabilitare la crittografia dei dischi per le macchine virtuali IaaS, seguire questa procedura generale:

1. Scegliere di disabilitare la crittografia (decrittografia) in una macchina virtuale IaaS in esecuzione in Azure e specificare la configurazione della decrittografia. È possibile disabilitare la crittografia tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure.

 Questo passaggio agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Come indicato nella sezione precedente, tuttavia, la disabilitazione della crittografia del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux, purché il disco del sistema operativo non sia crittografato.
2. Azure aggiorna il modello di servizi della macchina virtuale e la VM IaaS viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

> [!NOTE]
> L'operazione di disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per sistemi Windows o Passphrase per Linux.
 > La disabilitazione della crittografica del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux.
La disabilitazione della crittografia del disco dati per Linux non è supportata se l'unità del sistema operativo è crittografata.


## <a name="encryption-workflow-previous-release"></a>Flusso di lavoro di crittografia (versione precedente)

La nuova versione di Crittografia dischi di Azure elimina la necessità di specificare un parametro dell'applicazione Azure AD per abilitare la crittografia dei dischi per le macchine virtuali. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD. Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD. Per abilitare la crittografia dei dischi per macchine virtuali Windows e Linux (versione precedente), seguire questa procedura:

1. Scegliere uno scenario di crittografia tra gli scenari di crittografia precedenti.
2. Acconsentire esplicitamente per abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o un comando dell'interfaccia della riga di comando e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale IaaS.
   * Per le nuove macchine virtuali create dal Marketplace e per le macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella VM IaaS.

3. Concedere l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS.

4. Specificare l'identità dell'applicazione di Azure Active Directory (Azure AD) per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Questa operazione abilita la crittografia nella macchina virtuale IaaS per gli scenari indicati nel passaggio 2.

5. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.


## <a name="terminology"></a>Terminologia
Per informazioni su alcuni dei termini comuni usati da questa tecnologia, vedere la tabella terminologica seguente:

| Terminologia | Definizione |
| --- | --- |
| Azure AD | Azure AD è l'abbreviazione di [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Un account Azure AD è usato per le operazioni di autenticazione, archiviazione e recupero dei segreti da un insieme di credenziali delle chiavi. |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards), che consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia dei dischi nelle macchine virtuali IaaS di Windows. |
| BEK | Le chiavi di crittografia BitLocker vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BitLocker sono protette nell'insieme di credenziali delle chiavi come segreti. |
| CLI | Vedere [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali IaaS Linux. |
| KEK | La chiave di crittografia della chiave (KEK, Key Encryption Key) è la chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o per eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PS | Vedere [Azure PowerShell cmdlets](/powershell/azure/overview) (Cmdlet di Azure PowerShell). |

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Prerequisiti di Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md)
