<properties
	pageTitle="Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima) | Microsoft Azure"
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
	ms.date="08/03/2016"
	ms.author="robinsh"/>  

# Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)

Crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima della persistenza nella risorsa di archiviazione e di decrittografarli prima del recupero. La crittografia, la decrittografia e la gestione delle chiavi sono completamente trasparenti per gli utenti.

Le sezioni seguenti forniscono indicazioni dettagliate su come usare le funzionalità Crittografia del servizio di archiviazione, oltre a informazioni sugli scenari supportati e sulle esperienze utente.

## Panoramica


Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. È possibile proteggere i dati in transito tra un'applicazione e Azure usando la [crittografia lato client](storage-client-side-encryption.md), HTTPS o SMB 3.0. Crittografia del servizio di archiviazione è una nuova funzionalità di Archiviazione di Azure che consente di crittografare i dati quando vengono scritti in Archiviazione di Azure e supporta i BLOB in blocchi, i BLOB di pagine e i BLOB di aggiunta. Questa funzionalità può essere abilitata per nuovi account di archiviazione usando il modello di distribuzione Azure Resource Manager ed è disponibile per tutti i livelli di ridondanza, ad esempio LRS (archiviazione con ridondanza locale), ZRS, GRS (archiviazione con ridondanza geografica) e RA-GRS. Crittografia del servizio di archiviazione è disponibile per l'Archiviazione Standard e Premium e gestisce la crittografia, la decrittografia e la gestione delle chiavi in modo completamente trasparente. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili. La sezione Anteprima seguente include informazioni dettagliate su come partecipare al programma di anteprima per Crittografia del servizio di archiviazione.

Questa schermata mostra dove trovare l'impostazione relativa a Crittografia del servizio di archiviazione usando il [portale di Azure](https://azure.portal.com). In questa schermata occorre fare clic su Crittografia per continuare.

![Schermata del portale che mostra l'opzione Crittografia](./media/storage-service-encryption/image1.png)

Dopo avere selezionato l'impostazione Crittografia, è possibile abilitare o disabilitare Crittografia del servizio di archiviazione.

![Schermata del portale che mostra le proprietà della crittografia](./media/storage-service-encryption/image2.png)

##Disponibilità

Per l'archiviazione Standard, questa funzionalità è attualmente disponibile nelle seguenti aree: Australia sud-orientale, Canada centrale, Canada orientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Europa occidentale e Stati Uniti occidentali.

Per l'archiviazione Premium, questa funzionalità è attualmente disponibile nelle seguenti aree: Australia sud-orientale, Canada centrale, Canada orientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale e Stati Uniti occidentali.

Questo documento verrà aggiornato nel corso della distribuzione della funzionalità in altre aree.

##Scenari di crittografia

La funzionalità Crittografia del servizio di archiviazione può essere abilitata a livello di account di archiviazione. Supporta gli scenari cliente seguenti:

-   Crittografia di BLOB in blocchi, BLOB di aggiunta e BLOB di pagine.

-   Crittografia di VHD archiviati e modelli trasferiti in Azure da posizioni locali.

-   Crittografia del sistema operativo sottostante e dei dischi dati per VM IaaS create usando i VHD.

L'anteprima pubblica presenta le limitazioni seguenti:

-   La crittografia degli account di archiviazione classici non è supportata.

-   La crittografia degli account di archiviazione classici di cui è stata eseguita la migrazione negli account di archiviazione di Resource Manager non è supportata.

-   Dati esistenti: Crittografia del servizio di archiviazione crittografa solo i dati appena creati dopo l'abilitazione della crittografia. Se ad esempio si crea un nuovo account di archiviazione di Resource Manager ma non si attiva la crittografia e quindi si caricano BLOB o VHD archiviati nell'account di archiviazione e in seguito si attiva Crittografia del servizio di archiviazione, questi BLOB non verranno crittografati, a meno che non vengano riscritti o copiati.

-   Supporto per il Marketplace: è possibile abilitare la crittografia di VM create dal Marketplace usando il portale di Azure [https://portal.azure.com), PowerShell e l'interfaccia della riga di comando di Azure. L'immagine di base del VHD rimarrà non crittografata. Eventuali operazioni di scrittura eseguite dopo la creazione della macchina virtuale, tuttavia, verranno crittografate.

-   Le tabelle, le code e i dati dei file non verranno crittografati.

##Preview

Questa funzionalità è supportata solo per gli account di archiviazione di Resource Manager. Gli account di archiviazione classici non sono supportati. Per usare questa nuova funzionalità, è necessario registrare la sottoscrizione usando i cmdlet di PowerShell. Dopo l'approvazione della sottoscrizione sarà possibile abilitare Crittografia del servizio di archiviazione per l'account di archiviazione disponibile nella sottoscrizione approvata. Analogamente alla maggior parte delle anteprime, è consigliabile non usare questa anteprima per i carichi di lavoro di produzione fino a quando la funzionalità non sarà disponibile a livello generale. È possibile partecipare al gruppo relativo all'anteprima di Crittografia del servizio di archiviazione su Yammer per inviare commenti e suggerimenti relativi all'esperienza specifica.

### Registrazione all'anteprima

-   [Installare i cmdlet di Azure PowerShell](../powershell-install-configure.md).

-   In Windows 10 aprire PowerShell come amministratore.

-   Effettuare la registrazione nello spazio dei nomi del provider di risorse di archiviazione. Questa operazione è necessaria solo per una sottoscrizione non ancora registrata nel provider di risorse di archiviazione.

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   Per registrarsi alla funzionalità, è possibile usare il cmdlet Register-AzureRmProviderFeature di PowerShell.

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   Per eseguire query relative allo stato della registrazione, in modo da verificare se la sottoscrizione è stata approvata, è possibile usare il cmdlet Get-AzureRmProviderFeature di PowerShell.

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

Quando per lo stato della registrazione viene restituito il valore "Registrata", la sottoscrizione è stata approvata. Visitare anche il gruppo relativo all'anteprima di Crittografia del servizio di archiviazione di Azure su Yammer.

##Introduzione

###Passaggio 1: [Iscriversi per l'anteprima](#registering-for-preview).

###Passaggio 2: [Creare un nuovo account di archiviazione](storage-create-storage-account.md).

###Passaggio 3: Abilitare la crittografia.

È possibile abilitare la crittografia usando il [portale di Azure](https://portal.azure.com).

> [AZURE.NOTE] Se si vuole abilitare o disabilitare a livello di codice la crittografia del servizio di archiviazione in un account di archiviazione, è possibile usare l'[API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx). Questa funzionalità verrà presto aggiunta alla [libreria client del provider di risorse di archiviazione per .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), ad Azure PowerShell e all'interfaccia della riga di comando di Azure.

###Passaggio 4: Copiare i dati in un account di archiviazione

#### Con AzCopy

AzCopy è un'utilità della riga di comando di Windows progettata la copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure usando semplici comandi con prestazioni ottimali. È possibile usare questa utilità per copiare dati da un account di archiviazione BLOB esistente in un nuovo account di archiviazione in cui è abilitata la funzionalità di crittografia.

Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### Con le librerie dei client di archiviazione

È possibile caricare e scaricare dati da e nelle risorse di archiviazione BLOB usando il set avanzato di librerie dei client di archiviazione, inclusi .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md).

#### Con Storage Explorer

È possibile usare uno strumento di esplorazione di archiviazione per creare account di archiviazione, caricare e scaricare i dati, visualizzare i contenuti dei BLOB e spostarsi tra le directory. Molti supportano account di archiviazione classici e di Resource Manager.

È possibile usare uno di questi strumenti per caricare BLOB nell'account di archiviazione con crittografia abilitata. Nel caso di alcuni strumenti di esplorazione di archiviazione è anche possibile copiare i dati dall'account di archiviazione esistente a un nuovo account di archiviazione per cui è abilitata la funzionalità Crittografia del servizio di archiviazione.

Per altre informazioni, vedere la pagina relativa agli [strumenti di esplorazione di archiviazione di Azure](storage-explorers.md).

###Passaggio 5: Eseguire query relative allo stato dei dati crittografati

Quando Crittografia del servizio di archiviazione sarà disponibile a livello generale, verrà distribuita una versione aggiornata delle librerie dei client di archiviazione che consentirà di eseguire query relative allo stato di un oggetto per determinare se è crittografato o meno.

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

R: No, la funzionalità Crittografia del servizio di archiviazione è supportata solo negli account di archiviazione di Resource Manager appena creati in anteprima.

**D: Come si crittografano i dati nell'account di archiviazione classico?**

R: È possibile creare un nuovo account di archiviazione di Resource Manager e copiare i dati usando [AzCopy](storage-use-azcopy.md) dall'account di archiviazione classico esistente all'account di archiviazione di Resource Manager appena creato.

**D: È disponibile un account di archiviazione di Resource Manager. Si può abilitare Crittografia del servizio di archiviazione per questo account?**

R: Durante la disponibilità in anteprima di Crittografia del servizio di archiviazione è necessario creare un nuovo account per potere accedere a questa nuova funzionalità.

**D: È possibile crittografare i dati correnti in un account di archiviazione di Resource Manager esistente?**

R: Se l'account di archiviazione di Resource Manager esistente è stato creato prima di questo annuncio relativo alla versione di anteprima, è possibile creare un nuovo account di archiviazione di Resource Manager e abilitare la crittografia. È quindi possibile copiare i dati dall'account di archiviazione precedente. I dati verranno crittografati automaticamente. Se, tuttavia, l'account di archiviazione di Resource Manager è stato creato dopo l'annuncio relativo alla versione di anteprima e si è deciso di abilitare la crittografia in un momento successivo, è possibile abilitare la crittografia per questo account di archiviazione usando il portale di Azure, quindi riscrivere i dati non crittografati nell'account di archiviazione.

**D: È possibile usare Crittografia del servizio di archiviazione se si usa l'Archiviazione Premium?**

R: Sì, la funzionalità Crittografia del servizio di archiviazione è supportata nell'Archiviazione Standard e nell'Archiviazione Premium.

**D: Se si crea un nuovo account di archiviazione e si abilita Crittografia del servizio di archiviazione, quindi si crea una nuova VM usando tale account di archiviazione, la VM sarà crittografata?**

A: Sì. Eventuali dischi creati che usano il nuovo account di archiviazione verranno crittografati, purché vengano creati dopo l'abilitazione di Crittografia del servizio di archiviazione. Se la VM è stata creata usando Azure Marketplace, l'immagine di base del VHD rimarrà non crittografata. Eventuali operazioni di scrittura eseguite dopo la creazione della macchina virtuale, tuttavia, verranno crittografate.

**D: È possibile creare nuovi account di archiviazione con la funzionalità Crittografia del servizio di archiviazione abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**

R: Questa funzionalità sarà disponibile nelle prossime versioni di Azure PowerShell e dell'interfaccia della riga di comando di Azure, attualmente previste per la fine di aprile.

**D: A quanto ammonta il costo aggiuntivo dell'Archiviazione di Azure se si abilita Crittografia del servizio di archiviazione?**

R: Non sono previsti costi aggiuntivi.

**D: Come ci si iscrive all'anteprima?**

R: È possibile registrarsi per ottenere l'accesso all'anteprima usando PowerShell. Dopo l'approvazione della sottoscrizione per la funzionalità, sarà possibile usare PowerShell per abilitare la crittografia dei dati inattivi.

**D: Quando ci si iscrive all'anteprima usando PowerShell, quale nome di funzionalità occorre selezionare per la registrazione?**

R: EncryptionAtRest.

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

R: Quando è stato creato l'account di archiviazione? Durante la fase di anteprima, sarà necessario registrare la sottoscrizione e creare anche un nuovo account di archiviazione per potere usare Crittografia del servizio di archiviazione. Non è possibile abilitare Crittografia del servizio di archiviazione su account di archiviazione creati prima della disponibilità in anteprima.

**D: L'anteprima di Crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

R: L'anteprima di Crittografia del servizio di archiviazione è disponibile in Asia orientale ed Europa occidentale per l'Archiviazione Standard e in Giappone orientale per l'Archiviazione Premium. Questo documento verrà aggiornato nel corso della distribuzione di questa funzionalità in altre aree nei prossimi mesi.

**D: Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**

D: Contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per qualsiasi problema relativo a Crittografia del servizio di archiviazione.

##Passaggi successivi

Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).

<!---HONumber=AcomDC_0810_2016-->