---
title: Crittografia del servizio di archiviazione di Azure per dati inattivi | Microsoft Docs
description: "La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare l'archivio BLOB di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Crittografia del servizio di archiviazione di Azure per dati inattivi
Crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima della persistenza nella risorsa di archiviazione e di decrittografarli prima del recupero. La crittografia, la decrittografia e la gestione delle chiavi sono completamente trasparenti per gli utenti.

Le sezioni seguenti forniscono indicazioni dettagliate su come usare le funzionalità Crittografia del servizio di archiviazione, oltre a informazioni sugli scenari supportati e sulle esperienze utente.

## <a name="overview"></a>Panoramica
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. È possibile proteggere i dati in transito tra un'applicazione e Azure usando la [crittografia lato client](../storage-client-side-encryption.md), HTTPS o SMB 3.0. La crittografia del servizio di archiviazione permette di eseguire la crittografia a riposo, gestendo le operazioni di crittografia, decrittografia e gestione delle chiavi in modo completamente trasparente. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

La crittografia del servizio di archiviazione esegue la crittografia dei dati durante la scrittura in Archiviazione di Azure e può essere usata per Archiviazione BLOB di Azure e l'archiviazione file. È adatta alle operazioni seguenti:

* Archiviazione standard: account di archiviazione di uso generico per i BLOB, account di archiviazione file e account di archiviazione BLOB
* Archiviazione Premium 
* Tutti i livelli di ridondanza (LRS, ZRS, GRS e RA-GRS)
* Account di archiviazione di Azure Resource Manager (non classici) 
* Tutte le aree.

Per altre informazioni, vedere le domande frequenti.

Per abilitare o disabilitare la crittografia del servizio di archiviazione per un account di archiviazione, accedere al [portale di Azure](https://portal.azure.com) e selezionare un account di archiviazione. Nel pannello delle impostazioni cercare la sezione Servizio BLOB, illustrata in questo screenshot, e fare clic su Crittografia.

![Screenshot del portale che illustra l'opzione Crittografia](./media/storage-service-encryption/image1.png)
<br/>*Figura 1: abilitare la crittografia del servizio di archiviazione per il servizio BLOB (passaggio 1)*

![Screenshot del portale che illustra l'opzione Crittografia](./media/storage-service-encryption/image3.png)
<br/>*Figura 2: abilitare la crittografia del servizio di archiviazione per il servizio file (passaggio 1)*

Dopo avere selezionato l'impostazione Crittografia, è possibile abilitare o disabilitare Crittografia del servizio di archiviazione.

![Screenshot del portale che illustra le proprietà della crittografia](./media/storage-service-encryption/image2.png)
<br/>*Figura 3: abilitare la crittografia del servizio di archiviazione per il servizio BLOB e file (passaggio 2)*

## <a name="encryption-scenarios"></a>Scenari di crittografia
La funzionalità Crittografia del servizio di archiviazione può essere abilitata a livello di account di archiviazione. Dopo l'abilitazione della funzionalità, i clienti sceglieranno i servizi da crittografare. Supporta gli scenari cliente seguenti:

* Crittografia di archiviazione BLOB e archiviazione file negli account di Resource Manager.
* Crittografia dei servizi BLOB e file in account di archiviazione classici di cui è stata eseguita la migrazione negli account di archiviazione di Resource Manager.

La crittografia del servizio di archiviazione presenta le limitazioni seguenti:

* La crittografia degli account di archiviazione classici non è supportata.
* Dati esistenti: Crittografia del servizio di archiviazione crittografa solo i dati appena creati dopo l'abilitazione della crittografia. Se ad esempio si crea un nuovo account di archiviazione di Resource Manager, ma non si attiva la crittografia e quindi si caricano BLOB o VHD archiviati nell'account di archiviazione e in seguito si attiva Crittografia del servizio di archiviazione, questi BLOB non verranno crittografati, a meno che non vengano riscritti o copiati.
* Supporto per il Marketplace: è possibile abilitare la crittografia di VM create dal Marketplace usando il [portale di Azure](https://portal.azure.com), PowerShell e l'interfaccia della riga di comando di Azure. L'immagine di base del VHD rimarrà non crittografata. Eventuali operazioni di scrittura eseguite dopo la creazione della macchina virtuale, tuttavia, verranno crittografate.
* I dati di tabelle e code non verranno crittografati.

## <a name="getting-started"></a>Introduzione
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passaggio 1: [Creare un nuovo account di archiviazione](../storage-create-storage-account.md).
### <a name="step-2-enable-encryption"></a>Passaggio 2: Abilitare la crittografia.
È possibile abilitare la crittografia usando il [portale di Azure](https://portal.azure.com).

> [!NOTE]
> Se si vuole abilitare o disabilitare a livello di codice la crittografia del servizio di archiviazione in un account di archiviazione, è possibile usare l'[API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [libreria client dei provider delle risorse di archiviazione per .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o l'[interfaccia della riga di comando di Azure](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Passaggio 3: Copiare i dati in un account di archiviazione
Se si abilita la crittografia del servizio di archiviazione per il servizio BLOB, qualsiasi BLOB scritto su tale account di archiviazione verrà crittografato. Eventuali BLOB già presenti nell'account di archiviazione non verranno crittografati finché non saranno riscritti. È possibile copiare i dati da un account di archiviazione a uno con la crittografia del servizio di archiviazione eseguita oppure abilitare la crittografia del servizio di archiviazione e copiare i BLOB da un contenitore a un altro per assicurarsi che i dati precedenti siano crittografati. Per farlo, è possibile usare uno qualsiasi dei seguenti strumenti. La stessa operazione è applicabile ad Archiviazione file.

#### <a name="using-azcopy"></a>Con AzCopy
AzCopy è un'utilità della riga di comando di Windows progettata la copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure usando semplici comandi con prestazioni ottimali. Può essere usato per copiare i BLOB o i file da un account di archiviazione a un altro con la crittografia del servizio di archiviazione abilitata. 

Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### <a name="using-smb"></a>Con SMB
File di Azure offre condivisioni file nel cloud usando il protocollo SMB standard. È possibile montare una condivisione di file da un client in locale o in Azure. Dopo aver montato la condivisione è possibile usare strumenti come Robocopy per copiare i file in condivisioni file di Azure. Per altre informazioni, vedere [Come montare condivisioni file di Azure in Windows](../files/storage-how-to-use-files-windows.md) e [Come montare condivisioni file di Azure in Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>Con le librerie dei client di archiviazione
È possibile copiare i dati dei BLOB o dei file da e negli archivi BLOB o fra gli account di archiviazione usando il set avanzato di librerie dei client di archiviazione, inclusi .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure con .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Con Storage Explorer
È possibile usare uno strumento di esplorazione dell'archivio per creare account di archiviazione, caricare e scaricare dati, visualizzare i contenuti dei BLOB e spostarsi tra le directory. È possibile usare uno di questi strumenti per caricare BLOB nell'account di archiviazione con crittografia abilitata. Nel caso di alcuni strumenti di esplorazione dell'archiviazione è anche possibile copiare dati dall'archivio BLOB esistente a un contenitore diverso dell'account di archiviazione oppure in un nuovo account di archiviazione con la crittografia del servizio di archiviazione abilitata.

Per altre informazioni, vedere la pagina relativa agli [strumenti di esplorazione di archiviazione di Azure](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>Passaggio 4: Eseguire query relative allo stato dei dati crittografati
È stata distribuita una versione aggiornata delle librerie dei client di archiviazione che consente di eseguire query relative allo stato di un oggetto per determinare se sua crittografato o meno. È attualmente disponibile solo per Archiviazione BLOB. È in programma il supporto per Archiviazione file. 

Nel frattempo è possibile chiamare l'operazione per il [recupero delle proprietà dell'account](https://msdn.microsoft.com/library/azure/mt163553.aspx) per verificare che la crittografia sia abilitata per l'account di archiviazione oppure visualizzare le proprietà dell'account di archiviazione nel portale di Azure.

## <a name="encryption-and-decryption-workflow"></a>Flusso di lavoro di crittografia e decrittografia
Ecco una breve descrizione del flusso di lavoro di crittografia/decrittografia:

* Il cliente abilita la crittografia sull'account di archiviazione.
* Quando il cliente scrive nuovi dati (PUT Blob, PUT Block, PUT Page, PUT File e così via) in Archiviazione BLOB o Archiviazione file, ogni operazione di scrittura viene crittografata con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.
* Quando il cliente deve accedere ai dati (GET Blob e così via), i dati vengono decrittografati automaticamente prima della restituzione all'utente.
* Se la crittografia è disabilitata, le nuove operazioni di scrittura non vengono più crittografate e i dati crittografati esistenti rimangono crittografati fino a quando non vengono riscritti dall'utente. Quando la crittografia è abilitata, le operazioni di scrittura in Archiviazione BLOB e Archiviazione file verranno sempre crittografate. Lo stato dei dati non cambia quando l'utente attiva/disattiva la crittografia per l'account di archiviazione.
* Tutte le chiavi di crittografia vengono archiviate, crittografate e gestite da Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Domande frequenti su Crittografia del servizio di archiviazione per i dati inattivi
**D: È disponibile un account di archiviazione classico. Si può abilitare Crittografia del servizio di archiviazione per questo account?**

R: No, la funzionalità di crittografia del servizio di archiviazione è supportata solo negli account di archiviazione di Resource Manager.

**D: Come si crittografano i dati nell'account di archiviazione classico?**

R: È possibile creare un nuovo account di archiviazione di Resource Manager e copiare i dati usando [AzCopy](storage-use-azcopy.md) dall'account di archiviazione classico esistente all'account di archiviazione di Resource Manager appena creato. 

Se si esegue la migrazione dell'account di archiviazione classico a un account di archiviazione di Resource Manager, l'operazione non è immediata, in quanto modifica il tipo dell'account ma non ha effetto sui dati esistenti. Eventuali nuovi dati verranno crittografati solo dopo l'attivazione della crittografia. Per altre informazioni, vedere [Platform Supported Migration of IaaS Resources from Classic to Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)(Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager). Si noti che questa operazione è supportata solo per i servizi BLOB e file.

**D: È disponibile un account di archiviazione di Resource Manager. Si può abilitare Crittografia del servizio di archiviazione per questo account?**

R: Sì, ma verranno crittografati solo i dati appena scritti. La funzionalità non è retroattiva e i dati già presenti non verranno crittografati. La funzionalità non è ancora supportata per l'anteprima di Archiviazione file.

**D: È possibile crittografare i dati correnti in un account di archiviazione di Resource Manager esistente?**

R: La crittografia del servizio di archiviazione può essere abilitata in qualsiasi momento in un account di archiviazione di Resource Manager. Tuttavia, i dati che erano già presenti non verranno crittografati. Per crittografare i dati esistenti, è possibile copiarli in un altro nome o in un altro contenitore e quindi rimuoverne le versioni non crittografate.

**D: È possibile usare Crittografia del servizio di archiviazione se si usa l'Archiviazione Premium?**

R: Sì, la funzionalità Crittografia del servizio di archiviazione è supportata nell'Archiviazione Standard e nell'Archiviazione Premium.  Archiviazione Premium non è supportata per il servizio file.

**D: Se si crea un nuovo account di archiviazione e si abilita Crittografia del servizio di archiviazione, quindi si crea una nuova VM usando tale account di archiviazione, la VM sarà crittografata?**

A: Sì. Eventuali dischi creati che usano il nuovo account di archiviazione verranno crittografati, purché vengano creati dopo l'abilitazione di Crittografia del servizio di archiviazione. Se la VM è stata creata usando Azure Marketplace, l'immagine di base del VHD rimarrà non crittografata. Eventuali operazioni di scrittura eseguite dopo la creazione della macchina virtuale, tuttavia, verranno crittografate.

**D: È possibile creare nuovi account di archiviazione con la funzionalità Crittografia del servizio di archiviazione abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**

A: Sì.

**D: A quanto ammonta il costo aggiuntivo dell'Archiviazione di Azure se si abilita Crittografia del servizio di archiviazione?**

R: Non sono previsti costi aggiuntivi.

**D: Chi gestisce le chiavi di crittografia?**

R: Le chiavi vengono gestite da Microsoft.

**D: È possibile usare le proprie chiavi di crittografia?**

R: Le funzionalità per l'uso di chiavi di crittografia personalizzate dei clienti saranno presto disponibili.

**D: È possibile revocare l'accesso alle chiavi di crittografia?**

R: Non attualmente. Le chiavi vengono gestite completamente da Microsoft.

**D: La funzionalità Crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo account di archiviazione?**

R: La funzionalità Crittografia del servizio di archiviazione e non è abilitata per impostazione predefinita. È possibile usare il portale di Azure per abilitarla. È anche possibile abilitare questa funzionalità a livello di codice usando l'API REST del provider di risorse di archiviazione.

**D: Quali sono le differenze rispetto alla Crittografia dischi di Azure?**

R: Questa funzionalità viene usata per crittografare i dati nell'archivio BLOB di Azure. Crittografia dischi di Azure viene usata per crittografare i dischi del sistema operativo e i dischi dati nelle VM IaaS. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](../storage-security-guide.md).

**D: Cosa succede se si abilita Crittografia del servizio di archiviazione e quindi si abilita Crittografia dischi di Azure sui dischi?**

R: Non si verifica alcun problema. I dati verranno crittografati da entrambi i metodi.

**D: L'account di archiviazione è configurato per la replica con ridondanza geografica. Se si abilita la crittografia del sistema di archiviazione, verrà crittografata anche la copia ridondante?**

R: Sì, tutte le copie dell'account di archiviazione vengono crittografate e sono supportate tutte le opzioni di ridondanza, ovvero archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

**D: Non è possibile abilitare la crittografia sull'account di archiviazione.**

R: Se si tratta di un account di archiviazione di Resource Manager, gli account di archiviazione di tipo classico non sono supportati. 

**D: La crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

R: La crittografia del servizio di archiviazione è disponibile in tutte le aree per Archiviazione BLOB. Per Archiviazione file, verificare la sezione relativa alla disponibilità. 

**D: Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**

D: Contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per qualsiasi problema relativo a Crittografia del servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](../storage-security-guide.md).

