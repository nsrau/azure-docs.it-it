<properties
	pageTitle="Crittografia del servizio di archiviazione di Azure per dati inattivi | Microsoft Azure"
	description="La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare l'archivio BLOB di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>  

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="lakasa;robinsh"/>  

# Crittografia del servizio di archiviazione di Azure per dati inattivi

Crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima della persistenza nella risorsa di archiviazione e di decrittografarli prima del recupero. La crittografia, la decrittografia e la gestione delle chiavi sono completamente trasparenti per gli utenti.

Le sezioni seguenti forniscono indicazioni dettagliate su come usare le funzionalità Crittografia del servizio di archiviazione, oltre a informazioni sugli scenari supportati e sulle esperienze utente.

## Panoramica

Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. È possibile proteggere i dati in transito tra un'applicazione e Azure usando la [crittografia lato client](storage-client-side-encryption.md), HTTPS o SMB 3.0. La crittografia del servizio di archiviazione permette di eseguire la crittografia a riposo, gestendo le operazioni di crittografia, decrittografia e gestione delle chiavi in modo completamente trasparente. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

La crittografia del servizio di archiviazione esegue la crittografia dei dati durante la scrittura in Archiviazione di Azure e può essere utilizzata per i BLOB in blocchi, i BLOB di pagine e i BLOB di aggiunta. È adatta alle operazioni seguenti:

-   Account di archiviazione di uso generale e account di archiviazione BLOB
-   Archiviazione Standard e Archiviazione Premium
-   Tutti i livelli di ridondanza (LRS, ZRS, GRS e RA-GRS)
-   Account di archiviazione di Azure Resource Manager (non classici)
-   Tutte le aree

Per abilitare o disabilitare la crittografia del servizio di archiviazione per un account di archiviazione, accedere al [portale di Azure](https://azure.portal.com) e selezionare un account di archiviazione. Nel pannello delle impostazioni cercare la sezione Servizio BLOB, illustrata in questo screenshot, e fare clic su Crittografia.

![Schermata del portale che mostra l'opzione Crittografia](./media/storage-service-encryption/image1.png)  

Dopo avere selezionato l'impostazione Crittografia, è possibile abilitare o disabilitare Crittografia del servizio di archiviazione.

![Schermata del portale che mostra le proprietà della crittografia](./media/storage-service-encryption/image2.png)  

##Scenari di crittografia

La funzionalità Crittografia del servizio di archiviazione può essere abilitata a livello di account di archiviazione. Supporta gli scenari cliente seguenti:

-   Crittografia di BLOB in blocchi, BLOB di aggiunta e BLOB di pagine.

-   Crittografia di VHD archiviati e modelli trasferiti in Azure da posizioni locali.

-   Crittografia del sistema operativo sottostante e dei dischi dati per VM IaaS create usando i VHD.

La crittografia del servizio di archiviazione presenta le limitazioni seguenti:

-   La crittografia degli account di archiviazione classici non è supportata.

-   La crittografia degli account di archiviazione classici di cui è stata eseguita la migrazione negli account di archiviazione di Resource Manager non è supportata.

-   Dati esistenti: Crittografia del servizio di archiviazione crittografa solo i dati appena creati dopo l'abilitazione della crittografia. Se ad esempio si crea un nuovo account di archiviazione di Resource Manager ma non si attiva la crittografia e quindi si caricano BLOB o VHD archiviati nell'account di archiviazione e in seguito si attiva Crittografia del servizio di archiviazione, questi BLOB non verranno crittografati, a meno che non vengano riscritti o copiati.

-   Supporto per il Marketplace: è possibile abilitare la crittografia di VM create dal Marketplace usando il [portale di Azure](https://portal.azure.com), PowerShell e l'interfaccia della riga di comando di Azure. L'immagine di base del VHD rimarrà non crittografata. Eventuali operazioni di scrittura eseguite dopo la creazione della macchina virtuale, tuttavia, verranno crittografate.

-   Le tabelle, le code e i dati dei file non verranno crittografati.

##Introduzione

###Passaggio 1: [Creare un nuovo account di archiviazione](storage-create-storage-account.md).

###Passaggio 2: Abilitare la crittografia.

È possibile abilitare la crittografia usando il [portale di Azure](https://portal.azure.com).

> [AZURE.NOTE] Se si vuole abilitare o disabilitare a livello di codice la crittografia del servizio di archiviazione in un account di archiviazione, è possibile usare l'[API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx) la [libreria di client dei provider delle risorse di archiviazione per .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando di Azure](storage-azure-cli.md).

###Passaggio 3: Copiare i dati in un account di archiviazione

Se si abilita la crittografia del servizio di archiviazione su un account in cui successivamente si scrivono BLOB, questi ultimi verranno crittografati. Eventuali BLOB già presenti nell'account di archiviazione non verranno crittografati finché non saranno riscritti. È possibile copiare i dati da un account di archiviazione a uno con la crittografia del servizio di archiviazione eseguita oppure abilitare la crittografia del servizio di archiviazione e copiare i BLOB da un contenitore a un altro per assicurarsi che i dati precedenti siano crittografati. Per farlo, è possibile usare uno qualsiasi dei seguenti strumenti.

#### Con AzCopy

AzCopy è un'utilità della riga di comando di Windows progettata la copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure usando semplici comandi con prestazioni ottimali. Può essere usato per copiare i BLOB da un account di archiviazione a un altro con la crittografia del servizio di archiviazione abilitata.

Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### Con le librerie dei client di archiviazione

È possibile copiare i dati dei BLOB da e negli archivi BLOB o fra gli account di archiviazione usando il set avanzato di librerie dei client di archiviazione, inclusi .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md).

#### Con Storage Explorer

È possibile usare uno strumento di esplorazione dell'archivio per creare account di archiviazione, caricare e scaricare dati, visualizzare i contenuti dei BLOB e spostarsi tra le directory. È possibile usare uno di questi strumenti per caricare BLOB nell'account di archiviazione con crittografia abilitata. Nel caso di alcuni strumenti di esplorazione dell'archiviazione è anche possibile copiare dati dall'archivio BLOB esistente a un contenitore diverso dell'account di archiviazione oppure in un nuovo account di archiviazione con la crittografia del servizio di archiviazione abilitata.

Per altre informazioni, vedere la pagina relativa agli [strumenti di esplorazione di archiviazione di Azure](storage-explorers.md).

###Passaggio 4: Eseguire query relative allo stato dei dati crittografati

È stata distribuita una versione aggiornata delle librerie dei client di archiviazione che consente di eseguire query relative allo stato di un oggetto per determinare se sua crittografato o meno. A breve verranno aggiunti alcuni esempi in questo documento.

Nel frattempo è possibile chiamare l'operazione per il [recupero delle proprietà dell'account](https://msdn.microsoft.com/library/azure/mt163553.aspx) per verificare che la crittografia sia abilitata per l'account di archiviazione oppure visualizzare le proprietà dell'account di archiviazione nel portale di Azure.

##Flusso di lavoro di crittografia e decrittografia

Ecco una breve descrizione del flusso di lavoro di crittografia/decrittografia:

-   Il cliente abilita la crittografia sull'account di archiviazione.

-   Quando il cliente scrive nuovi dati (PUT Blob, PUT Block, PUT Page e così via) nell'archiviazione BLOB, ogni operazione di scrittura viene crittografata mediante la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

-   Quando il cliente deve accedere ai dati (GET Blob e così via), i dati vengono decrittografati automaticamente prima della restituzione all'utente.

-   Se la crittografia è disabilitata, le nuove operazioni di scrittura non vengono più crittografate e i dati crittografati esistenti rimangono crittografati fino a quando non vengono riscritti dall'utente. Quando la crittografia è abilitata, le operazioni di scrittura nell'archivio BLOB verranno sempre crittografate. Lo stato dei dati non cambia quando l'utente attiva/disattiva la crittografia per l'account di archiviazione.

-   Tutte le chiavi di crittografia vengono archiviate, crittografate e gestite da Microsoft.

##Domande frequenti su Crittografia del servizio di archiviazione per i dati inattivi

**D: È disponibile un account di archiviazione classico. Si può abilitare Crittografia del servizio di archiviazione per questo account?**

R: No, la funzionalità di crittografia del servizio di archiviazione è supportata solo negli account di archiviazione di Resource Manager.

**D: Come si crittografano i dati nell'account di archiviazione classico?**

R: È possibile creare un nuovo account di archiviazione di Resource Manager e copiare i dati usando [AzCopy](storage-use-azcopy.md) dall'account di archiviazione classico esistente all'account di archiviazione di Resource Manager appena creato.

Un'altra opzione consiste nell'eseguire la migrazione dell'account di archiviazione classico in un account di archiviazione di Resource Manager. Per altre informazioni, vedere [Platform Supported Migration of IaaS Resources from Classic to Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/) (Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager).

**D: È disponibile un account di archiviazione di Resource Manager. Si può abilitare Crittografia del servizio di archiviazione per questo account?**

R: Sì, ma verranno crittografati solo i BLOB appena scritti. La funzionalità non è retroattiva e i dati già presenti non verranno crittografati.

**D: È possibile crittografare i dati correnti in un account di archiviazione di Resource Manager esistente?**

R: La crittografia del servizio di archiviazione può essere abilitata in qualsiasi momento in un account di archiviazione di Resource Manager. Tuttavia, i BLOB che erano già presenti non verranno crittografati. Per crittografare questi BLOB, è possibile copiarli in un altro nome o in un altro contenitore e quindi rimuoverne le versioni non crittografate.

**D: È possibile usare Crittografia del servizio di archiviazione se si usa l'Archiviazione Premium?**

R: Sì, la funzionalità Crittografia del servizio di archiviazione è supportata nell'Archiviazione Standard e nell'Archiviazione Premium.

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

**D: Quali sono le differenze rispetto alla crittografia delle unità di Azure?**

R: Questa funzionalità viene usata per crittografare i dati nell'archivio BLOB di Azure. Crittografia dischi di Azure viene usata per crittografare i dischi del sistema operativo e i dischi dati nelle VM IaaS. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).

**D: Cosa succede se si abilita Crittografia del servizio di archiviazione e quindi si abilita Crittografia dischi di Azure sui dischi?**

R: Non si verifica alcun problema. I dati verranno crittografati da entrambi i metodi.

**D: L'account di archiviazione è configurato per la replica con ridondanza geografica. Se si abilita la crittografia del sistema di archiviazione, verrà crittografata anche la copia ridondante?**

R: Sì, tutte le copie dell'account di archiviazione vengono crittografate e sono supportate tutte le opzioni di ridondanza, ovvero archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

**D: Non è possibile abilitare la crittografia sull'account di archiviazione.**

R: Se si tratta di un account di archiviazione di Resource Manager, gli account di archiviazione di tipo classico non sono supportati.

**D: La crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

R: La crittografia del servizio di archiviazione è disponibile in tutte le aree geografiche.

**D: Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**

D: Contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per qualsiasi problema relativo a Crittografia del servizio di archiviazione.

##Passaggi successivi

Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).

<!---HONumber=AcomDC_0928_2016-->