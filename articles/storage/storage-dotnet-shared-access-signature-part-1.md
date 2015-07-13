<properties 
	pageTitle="Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso SAS | Microsoft Azure" 
	description="Informazioni sulla delega dell'accesso a risorse di tipo BLOB, coda e tabella con firme di accesso condiviso." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>



# Firme Accesso Condiviso, Parte 1: Comprensione del Modello SAS

## Panoramica

Una firma di accesso condiviso costituisce un potente strumento per concedere ad altri client accesso limitato a BLOB, tabelle e code dell'account di archiviazione, senza dover esporre la chiave dell'account. Nella parte 1 di questa esercitazione sulle firme di accesso condiviso verranno fornite informazioni di carattere generale sul modello della firma di accesso condiviso e ne verranno esaminate le procedure consigliate. [Nella ](storage-dotnet-shared-access-signature-part-2.md)parte 2 dell'esercitazione verrà invece illustrato il processo di creazione delle firme di accesso condiviso tramite il servizio BLOB.

## Informazioni sulla firma di accesso condiviso ##

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato.

## Perché usare una firma di accesso condiviso ##

È possibile usare una firma di accesso condiviso quando si desidera fornire l'accesso alle risorse dell'account di archiviazione a un client al quale non si desidera fornire la chiave dell'account. Le chiavi dell'account di archiviazione includono una chiave primaria e una chiave secondaria, che garantiscono entrambi accesso amministrativo all'account e a tutte le risorse in esso presenti. Se si espone una delle chiavi dell'account, è possibile che l'account venga utilizzato in modo dannoso o non appropriato. Le firme di accesso condiviso costituiscono un'alternativa sicura per consentire ad altri client di leggere, scrivere ed eliminare dati nell'account di archiviazione sulla base delle autorizzazioni concesse e senza richiedere la chiave dell'account.

Uno scenario comune in cui la firma di accesso condiviso risulta utile è costituito da un servizio in cui gli utenti leggono e scrivono i propri dati nell'account di archiviazione di un utente specifico. Uno scenario in cui i dati utente vengono archiviati nell'account di archiviazione è caratterizzato da due modelli di progettazione standard:


1. I client caricano e scaricano dati tramite un servizio proxy front-end che esegue l'autenticazione. Tale servizio presenta il vantaggio di consentire la convalida delle regole di business, tuttavia per grandi quantità di dati o per transazioni con volumi elevati, la creazione di un servizio scalabile in base alla domanda può risultare oneroso o complesso.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2. Un servizio semplificato autentica il client in base alle necessità e quindi genera una firma di accesso condiviso. Dopo aver ricevuto la firma di accesso condiviso, il client può quindi accedere alle risorse dell'account di archiviazione direttamente con le autorizzazioni definite dalla firma e per l'intervallo consentito dalla firma. La firma di accesso condiviso consente di ridurre la necessità di instradare tutti i dati tramite il servizio proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

In molti servizi effettivi è possibile utilizzare una versione ibrida di questi due approcci, a seconda dello scenario interessato, in cui alcuni dati vengono elaborati e convalidati tramite il proxy front-end, mentre altri vengono salvati e/o letti direttamente tramite la firma di accesso condiviso.

## Funzionamento della firma di accesso condiviso ##

Una firma di accesso condiviso è un URI che punta a una risorsa di archiviazione e include uno speciale set di parametri di query che indicano la modalità di accesso alla risorsa da parte del client. Uno di questi parametri, ovvero la firma, viene creata dai parametri della firma di accesso condiviso e viene firmata con la chiave dell'account. Tale firma viene utilizzata dal servizio di archiviazione di Azure per autenticare la firma di accesso condiviso.

Di seguito sono elencati i vincoli usati per definire la firma di accesso condiviso e che vengono ciascuno rappresentati come parametro dell'URI:

- **Risorsa di archiviazione.** Le risorse di archiviazione per le quali è possibile delegare l'accesso includono contenitori, BLOB, code, tabelle e intervalli di entità tabella.
- **Ora di inizio.** Si riferisce all'ora in cui la firma di accesso condiviso diventa valida. L'ora di inizio di una firma di accesso condiviso è facoltativa; se omessa, la firma diventa immediatamente valida. 
- **Scadenza.** Si riferisce all'ora dopo la quale la firma di accesso condiviso non è più valida. Secondo le procedure consigliate è preferibile specificare una data di scadenza per una firma di accesso condiviso oppure associarla a criteri di accesso archiviati (vedere più avanti).
- **Autorizzazione.** Le autorizzazioni specificate per la firma di accesso condiviso indicano le autorizzazioni che il client può eseguire sulla risorsa di archiviazione usando la firma. 

Di seguito è riportato un esempio di URI di firma di accesso condiviso che fornisce autorizzazioni di lettura e scrittura a un BLOB. Nella tabella le singole parti dell'URI della firma di accesso condiviso vengono descritte separatamente per facilitarne la comprensione:

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    URI del BLOB
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Indirizzo del BLOB. Si noti che è vivamente consigliato l'utilizzo di HTTPS.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Versione dei servizi di archiviazione
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Per i servizi di archiviazione della versione 2012-02-12 e successive questo parametro indica la versione da usare.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Ora di inizio
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Specificata in un formato ISO 8061. Se si desidera che la firma di accesso condiviso sia immediatamente valida, omettere l'ora di inizio.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Scadenza
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Specificata in un formato ISO 8061.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Risorsa
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    La risorsa è un BLOB.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Autorizzazioni
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Le autorizzazioni concesse dalla firma di accesso condiviso includono lettura (r) e scrittura (w).
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Firma
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Utilizzata per autenticare l'accesso al BLOB. La firma è un HMAC calcolato sulla base di una stringa da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64.
                </p>
            </td>
        </tr>
    </tbody>
</table>


## Controllo delle firme di accesso condiviso con criteri di accesso archiviati ##

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS ad hoc:**quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, la scadenza e le autorizzazioni vengono tutte specificate nell'URI corrispondente oppure sono implicite, nel caso in cui l'ora di inizio viene omessa. È possibile creare questo tipo di firma di accesso condiviso per un contenitore, un BLOB, una tabella o una coda.
- **SAS con politica di accesso archiviazione:**i criteri di accesso archiviati vengono definiti per un contenitore di risorse, ovvero un contenitore BLOB, una tabella o una coda, e possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può usarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere utilizzata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1.	Viene raggiunta la scadenza specificata nella firma.
2.	Viene raggiunta la scadenza specificata nei criteri di accesso archiviati cui viene fatto riferimento nella firma (se viene fatto riferimento a criteri di accesso archiviati e se questi indicano una scadenza). Tale situazione può verificarsi alla scadenza dell'intervallo oppure perché i criteri di accesso archiviati sono stati modificati in modo che la scadenza ricorra nel passato e ciò corrisponde a un modo per revocare la firma di accesso condiviso.
3.	I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Si noti che se si ricreano i criteri di accesso archiviati con lo stesso nome, tutti i token esistenti della firma di accesso condiviso saranno ancora validi in base alle autorizzazioni associate ai criteri (presupponendo che non sia stata superata la scadenza indicata nella firma). Se si intende revocare la firma di accesso condiviso, assicurarsi di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.
4.	La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata. Si noti che in seguito a tale operazione tutti i componenti dell'applicazione che utilizzano la chiave dell'account non verranno più autenticati finché non verranno aggiornati in modo da usare l'altra chiave dell'account valida oppure la chiave dell'account appena rigenerata.
 
## Procedure consigliate per l'utilizzo delle firme di accesso condiviso ##

Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- In caso di diffusione di una firma di accesso condiviso, chiunque la ottiene può utilizzarla e questo potrebbe compromettere l'account di archiviazione.
- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperarne una nuova dal servizio, è possibile che le funzionalità dell'applicazione potrebbero risentirne.  

Per bilanciare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

1. **Usare sempre HTTPS** per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso passata tramite HTTP viene intercettata, un utente malintenzionato che esegue un attacco man-in-the-middle sarà in grado di leggere la firma e quindi di usarla come se fosse l'utente cui è destinata e questo potrebbe comportare la compromissione di dati sensibili o il danneggiamento dei dati da parte dell'utente malintenzionato.
2. **Laddove possibile fare riferimento a criteri di accesso archiviati.** I criteri di accesso archiviati consentono di revocare le autorizzazioni senza rigenerare le chiavi dell'account di archiviazione. Impostare la scadenza di questi criteri su un tempo molto lungo (o infinito) e assicurarsi che venga aggiornata regolarmente in modo che ricorra nel futuro.
3. **Usare scadenze a breve termine per una firma di accesso condiviso ad hoc.** In questo modo, anche se una firma di accesso condiviso viene compromessa inconsapevolmente, sarà valida solo per un breve periodo. Questo consiglio è particolarmente importante se non è possibile fare riferimento a criteri di accesso archiviati e consente inoltre di limitare la quantità di dati che è possibile scrivere in un BLOB riducendo il tempo disponibile per il caricamento.
4. **Se necessario, chiedere ai client di rinnovare automaticamente la firma di accesso condiviso..** I client devono rinnovare la firma di accesso condiviso prima della scadenza prevista al fine di disporre di tempo per ulteriori tentativi nel caso in cui il servizio che fornisce la firma non sia disponibile. Se la firma di accesso condiviso deve essere usata per un numero ridotto di operazioni immediate e di breve durata, che si prevede vengano completate entro la scadenza specificata, tale operazione potrebbe non essere necessaria in quanto non è previsto il rinnovo della firma. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. In questo caso è essenziale trovare un punto di equilibrio tale da garantire che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) e che il client richieda il rinnovo in tempo utile per evitare malfunzionamenti causati dalla scadenza della firma prima del rinnovo.
5. **Prestare attenzione all'ora di inizio della firma di accesso condiviso.** Se si imposta l'ora di inizio della firma di accesso condiviso su **ora**, a causa dello sfasamento di orario, ovvero delle differenze dell'ora corrente a seconda del computer in uso, potrebbero verificarsi problemi intermittenti nei primi minuti. In generale, impostare l'ora di inizio in modo che risalga ad almeno 15 minuti prima oppure non impostarla affatto, in modo che la firma diventi immediatamente valida in qualsiasi caso. Le stesse considerazioni sono valide anche per la scadenza, pertanto è consigliabile osservare fino a 15 minuti di sfasamento di orario in entrambe le direzioni di qualsiasi richiesta. Si noti che per i client che usano una versione di REST precedente alla 2012-02-12, la durata massima della firma di accesso condiviso che non fa riferimento a criteri di accesso archiviati è di 1 ora, pertanto tutti i criteri di durata maggiore non saranno validi.
6.	**Indicare in modo specifico la risorsa cui accedere.** La procedura consigliata di sicurezza standard consiste nel fornire a un utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Questo contribuirà a ridurre la minaccia di compromissione della firma di accesso condiviso in quanto la firma è meno potente nelle mani di un utente malintenzionato.
7.	**Comprendere che all'account verrà fatturato qualsiasi tipo di utilizzo, incluso quello effettuato con la firma di accesso condiviso.** Se si fornisce accesso in scrittura a un BLOB, un utente potrebbe scegliere di caricare un BLOB da 200 GB. Se poi si offre anche accesso in lettura, gli utenti potrebbero scegliere di scaricarlo 10 volte e ciò potrebbe comportare 2 TB di costi in uscita. Anche in questo caso, fornire autorizzazioni limitate per ridurre l'impatto potenziale di utenti malintenzionati. Per ridurre questa minaccia, usare firme di accesso condiviso di breve durata, prestando però attenzione allo sfasamento di orario per la scadenza.
8.	**Convalidare i dati scritti tramite la firma di accesso condiviso.** Quando un'applicazione client scrive i dati nell'account di archiviazione, tenere presente che tali dati potrebbero causare problemi. Se l'applicazione richiede che i dati vengano convalidati o autorizzati prima dell'uso, è necessario eseguire la convalida dopo la scrittura dei dati e prima che vengano usati dall'applicazione. Questa procedura consente inoltre di evitare la scrittura di dati danneggiati o dannosi nell'account da parte da un utente che ha acquisito correttamente la firma di accesso condiviso o di un utente che sfrutta una firma diffusa per errore.
9. **Non usare sempre SAS.** Talvolta i rischi associati a una particolare operazione su un account di archiviazione superano i benefici derivanti dall'uso della firma di accesso condiviso. Per tali operazioni creare un servizio di livello intermedio che effettui operazioni di scrittura nell'account di archiviazione dopo autenticazione, controllo e convalida di regole di business. Talvolta è inoltre più semplice gestire l'accesso in modi diversi. Se ad esempio si desidera rendere pubblicamente leggibili tutti i BLOB di un contenitore, è possibile rendere pubblico il contenitore anziché fornire una firma di accesso condiviso a ogni client per consentire l'accesso.
10.	**Usare Analisi archiviazione per monitorare l'applicazione.** È possibile usare la registrazione e la metrica per osservare eventuali picchi di errori di autenticazione causati da un'interruzione del servizio del provider di firme di accesso condiviso oppure alla rimozione accidentale di criteri di accesso archiviati. Per altre informazioni, vedere il [blog del team del servizio di archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Conclusioni ##

Le firme di accesso condiviso sono utili per offrire autorizzazioni limitate all'account di archiviazione ai client ai quali non si desidera fornire la chiave dell'account. In quanto tali, costituiscono una parte essenziale del modello di sicurezza di qualsiasi applicazione che utilizza il servizio di archiviazione di Azure. Il rispetto delle procedure consigliate sopra elencate consente di usare la firma di accesso condiviso per offrire accesso ancor più flessibile alle risorse dell'account di archiviazione senza compromettere la sicurezza dell'applicazione.

## Passaggi successivi ##

[Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con il servizio BLOB](../storage-dotnet-shared-access-signature-part-2/)

[Gestire l'accesso alle risorse di archiviazione di Azure](http://msdn.microsoft.com/library/azure/ee393343.aspx)

[Delega dell'accesso con una firma di accesso condiviso (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introduzione tabella e coda SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) [sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png [sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=July15_HO1-->