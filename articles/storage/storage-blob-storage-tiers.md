<properties
    pageTitle="Archiviazione ad accesso sporadico di Azure per i BLOB | Microsoft Azure"
    description="I livelli di archiviazione per l'archivio BLOB offrono un'archiviazione economicamente conveniente per dati oggetto in base ai modelli di accesso. L'archiviazione ad accesso sporadico di Azure è ottimizzata per i dati a cui si accede con minore frequenza."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico

## Panoramica

L'Archiviazione di Azure offre due livelli di archiviazione per l'archivio BLOB (archivio di oggetti), per consentire di archiviare i dati nel modo economicamente più conveniente in base alla modalità d'uso. Il **livello di archiviazione ad accesso frequente** di Azure è ottimizzato per l'archiviazione di dati a cui si accede di frequente. Il **livello di archiviazione ad accesso sporadico** di Azure è ottimizzato per l'archiviazione di dati a cui si accede di raramente e di lunga durata. I dati nel livello di archiviazione ad accesso sporadico possono tollerare una disponibilità leggermente più bassa, ma richiedono ugualmente una durabilità elevata e caratteristiche di velocità effettiva e tempo di accesso simili a quelle dei dati ad accesso frequente. Per i dati ad accesso sporadico, contratti di servizio con una disponibilità leggermente più bassa e costi di accesso più elevati sono compromessi accettabili in cambio di costi di archiviazione molto più bassi.

La quantità di dati archiviati nel cloud è attualmente in crescita esponenziale. Per gestire i costi per le esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato. I dati archiviati nel cloud possono essere piuttosto diversi dal punto di vista della generazione, dell'elaborazione e dell'accesso nel corso del tempo. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso molto frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione.

Tutti questi scenari di accesso ai dati descritti sopra usufruiscono di un livello differenziato di archiviazione ottimizzato per un particolare modello di accesso. L'introduzione dei livelli di archiviazione ad accesso frequente e sporadico consente all'archivio BLOB di Azure di soddisfare l'esigenza di livelli di archiviazione differenziati con modelli di determinazione dei prezzi diversi.

## Account di archiviazione BLOB

Gli **account di archiviazione BLOB** sono account di archiviazione specializzati per l'archiviazione dei dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Con gli account di archiviazione BLOB, ora è possibile scegliere tra i livelli di archiviazione ad accesso frequente e sporadico per archiviare i dati ad accesso sporadico a cui si accede meno spesso a un costo di archiviazione inferiore e per archiviare i dati ad accesso frequente a cui si accede più spesso a un costo di accesso inferiore. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico esistenti e condividono tutte le straordinarie funzionalità di durabilità, disponibilità, scalabilità e prestazioni già usate, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta.

> [AZURE.NOTE] Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine.

Gli account di archiviazione BLOB espongono l'attributo **Livello di accesso**, che consente di specificare il livello di accesso come **Frequente** o **Sporadico** a seconda dei dati archiviati nell'account. Se il modello di utilizzo dei dati cambia, è anche possibile passare da uno di questi livelli di accesso all'altro in qualsiasi momento.

> [AZURE.NOTE] La modifica del livello di accesso può comportare costi aggiuntivi. Per altri dettagli, vedere la sezione [Prezzi e fatturazione](storage-blob-storage-tiers.md#pricing-and-billing).

Gli scenari di utilizzo di esempio per il livello di archiviazione ad accesso frequente includono:

- Dati di uso attivo e o di cui è previsto l'accesso (lettura o scrittura) di frequente.
- Dati di gestione temporanea per l'elaborazione e l'eventuale migrazione al livello di archiviazione ad accesso sporadico.

Ecco alcuni scenari di utilizzo per il livello di archiviazione ad accesso sporadico:

- Set di dati di backup, archiviazione e ripristino di emergenza.
- Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
- Set di dati di grandi dimensioni che devono essere archiviati a costi contenuti mentre altri dati vengono raccolti per un'elaborazione futura, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati provenienti da una struttura di produzione.
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale, *ad esempio*, file multimediali non elaborati dopo la transcodifica in altri formati.
- Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai, *ad esempio*, filmati di videocamere di sicurezza, vecchie radiografie/risonanze magnetiche per le organizzazioni sanitarie, registrazioni audio e trascrizioni di chiamate di clienti per i servizi finanziari.

Per informazioni sugli account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

Per le applicazioni che richiedono solo l'archivio BLOB in blocchi o di aggiunta, è consigliabile usare gli account di archiviazione BLOB, per sfruttare il modello di determinazione prezzi differenziato dell'archiviazione a livelli. Questo tuttavia potrebbe non essere possibile in determinate circostanze in cui è meglio usare gli account di archiviazione di uso generico, ad esempio:

- È necessario usare tabelle, code o file e si vuole archiviare i BLOB nello stesso account di archiviazione. Si noti che l'unico vantaggio tecnico derivante dall'archiviarli nello stesso account è quello di avere le stesse chiavi condivise.
- È ancora necessario usare il modello di distribuzione classica. Gli account di archiviazione BLOB sono disponibili solo con il modello di distribuzione Azure Resource Manager.
- È necessario usare BLOB di pagine. Gli account di archiviazione BLOB non supportano i BLOB di pagine. A meno che non vi siano esigenze specifiche per usare i BLOB di pagine, è consigliabile usare i BLOB in blocchi.
- Si usa una versione dell'[API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) precedente alla 2014-02-14 o una libreria client con una versione precedente alla 4.x e non è possibile aggiornare l'applicazione.

> [AZURE.NOTE] Gli account di archiviazione BLOB sono attualmente supportati nella maggior parte delle aree di Azure, a cui se ne aggiungeranno altre in futuro. L'elenco aggiornato delle aree disponibili è riportato nella pagina [Servizi di Azure in base all'area](https://azure.microsoft.com/regions/#services).

## Confronto tra i livelli di accesso

La tabella seguente evidenzia il confronto tra i due livelli di accesso:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250"> <col width="250"> <col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Livello di archiviazione ad accesso frequente</center></strong></td>
    <td><strong><center>Livello di archiviazione ad accesso sporadico</center></strong>&lt;/td
</tr>
<tr>
    <td><strong><center>Disponibilità</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilità<br>(letture RA-GRS)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Addebiti per utilizzo</center></strong></td>
    <td><center>Costi di archiviazione più elevati<br>Costi di accesso e transazione più bassi</center></td>
    <td><center>Costi di archiviazione più bassi<br>Costi di accesso e transazione più elevati</center></td>
</tr>
<tr>
    <td><strong><center>Dimensioni minime oggetti<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Durata archiviazione minima<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latenza<br>(tempo per il primo byte)<center></strong></td>
    <td colspan="2"><center>millisecondi</center></td>
</tr>
<tr>
    <td><strong><center>Obiettivi di scalabilità e prestazioni<center></strong></td>
    <td colspan="2"><center>Uguali a quelli degli account di archiviazione di uso generico</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione di uso generico. Per ulteriori informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

## Prezzi e fatturazione

Gli account di archiviazione BLOB usano un nuovo modello di determinazione prezzi per l'archivio BLOB basato sul livello di accesso. Quando si usa un account di archiviazione BLOB, tenere conto delle considerazioni seguenti relative alla fatturazione:

- **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo dell'archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte è più basso per il livello di archiviazione ad accesso sporadico che per il livello di archiviazione ad accesso frequente.
- **Costi di accesso ai dati**: per i dati nel livello di archiviazione ad accesso sporadico, verrà addebitato un importo per l'accesso ai dati per gigabyte per le operazioni di lettura e scrittura.
- **Costi di transazione**: è previsto un addebito per ogni transazione per entrambi i livelli. Il costo per transazione per il livello di archiviazione ad accesso sporadico è tuttavia più elevato che per il livello di archiviazione ad accesso frequente.
- **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e RA-GRS. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.
- **Modifica del livello di accesso**: la modifica del livello di accesso da sporadico a frequente comporta un addebito corrispondente a quello per la lettura di tutti i dati esistenti nell'account di archiviazione per ogni transizione. Invece il passaggio del livello di accesso da frequente a non frequente sarà gratuito.

> [AZURE.NOTE] Per consentire agli utenti di provare i nuovi livelli di archiviazione e convalidare la funzionalità dopo l'avvio, l'addebito per il passaggio del livello di accesso da non frequente a frequente non verrà effettuato fino al 30 giugno 2016. A partire dal 1° luglio 2016, l'addebito verrà applicato a tutte le transizioni da Non frequente a Frequente. Per altri dettagli sul modello di determinazione prezzi per gli account di archiviazione BLOB, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altri dettagli sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## Avvio rapido

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

- Come creare un account di archiviazione BLOB.
- Come gestire un account di archiviazione BLOB.

### Uso del portale di Azure

#### Creare un account di archiviazione BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu Hub selezionare **Nuovo** -> **Dati e archiviazione** -> **Account di archiviazione**.

3. Immettere un nome per l'account di archiviazione.

4. Selezionare **Resource Manager** come modello di distribuzione.

5. Selezionare **Archiviazione BLOB** come tipo di account di archiviazione.

6. Selezionare il livello di accesso: **Frequente** o **Sporadico**. Il livello predefinito è **Frequente**.

7. Selezionare l'opzione di replica per l'account di archiviazione: **Archiviazione con ridondanza locale**, **Archiviazione con ridondanza geografica** o **Archiviazione con ridondanza geografica e accesso in lettura**. L'opzione predefinita è **Archiviazione con ridondanza geografica e accesso in lettura**. Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

8. Selezionare la sottoscrizione in cui si desidera creare il nuovo account di archiviazione.

9. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni sui gruppi di risorse, vedere [Uso del portale di Azure per gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

10. Selezionare la posizione geografica dell'account di archiviazione.

11. Fare clic su **Crea** per creare l'account di archiviazione.

#### Modificare il livello di accesso in un account di archiviazione BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'account di archiviazione.

2. Fare clic su **Tutte le impostazioni** e quindi fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

3. Specificare il livello di accesso preferito: **Frequente** o **Sporadico**.

    > [AZURE.NOTE] La modifica del livello di accesso può comportare costi aggiuntivi. Per altri dettagli, vedere la sezione [Prezzi e fatturazione](storage-blob-storage-tiers.md#pricing-and-billing).

## Migrazione agli account di archiviazione BLOB

Questa sezione descrive come eseguire una transizione senza problemi agli account di archiviazione BLOB. Un account di archiviazione BLOB serve per archiviare solo BLOB in blocchi e BLOB di aggiunta. Gli account di archiviazione di uso generico esistenti, che consentono di archiviare tabelle, code, file e dischi oltre ai BLOB, non possono essere convertiti in account di archiviazione BLOB. Per usare i livelli di archiviazione, sarà necessario creare nuovi account di archiviazione BLOB ed eseguire la migrazione dei dati esistenti negli account appena creati.

### Pianificazione della migrazione di dati esistenti

Se si intende spostare i dati in un account di archiviazione BLOB, sarà preferibile sfruttare i vantaggi del livello di archiviazione ad accesso sporadico per risparmiare sui costi di archiviazione per i dati usati meno di frequente. Il primo passaggio per pianificare la migrazione dei dati in un account di archiviazione BLOB al livello di archiviazione ad accesso sporadico consiste nel valutare il modello di utilizzo esistente per determinare se si trarrà vantaggio dalla migrazione a un account di archiviazione BLOB. In genere, è consigliabile conoscere:

- Il modello di utilizzo dell'archiviazione: quanti dati vengono archiviati e come cambia questo valore ogni mese?
- I modelli di accesso alle risorse di archiviazione: quanti dati vengono letti e scritti nell'account (inclusi i nuovi dati)? Quante e quali transazioni vengono usate per l'accesso ai dati?

Per monitorare gli account di archiviazione esistenti e per raccogliere questi dati, vedere [Abilitazione di Metriche di archiviazione di Azure e visualizzazione dei dati delle metriche](storage-enable-and-view-metrics.md). Con questi dati ora è possibile usare il [calcolatore prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.

### Migrazione di dati esistenti

È possibile usare i metodi seguenti per eseguire la migrazione dei dati esistenti agli account di archiviazione BLOB da dispositivi di archiviazione locali, da provider di archiviazione cloud di terze parti o dagli account di archiviazione di uso generico esistenti in Azure:

#### AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati nell'account di archiviazione BLOB dagli account di archiviazione di uso generico esistenti o per caricare i dati da dispositivi di archiviazione locali all'account di archiviazione BLOB.

Per altre dettagli, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimenti dati a prestazioni elevate, affidabili e semplici simili a quelle di AzCopy. In questo modo è possibile sfruttare pienamente i vantaggi delle funzionalità fornite da AzCopy nell'applicazione in modo nativo senza dover eseguire e monitorare istanze esterne di AzCopy.

Per altri dettagli, vedere la pagina relativa alla [libreria di spostamento dei dati di Archiviazione di Azure per .NET](https://github.com/Azure/azure-storage-net-data-movement)

#### API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati all'account di archiviazione BLOB usando una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altri dettagli, vedere [Introduzione all'archivio BLOB di Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] I BLOB crittografati mediante la crittografia lato client archiviano i metadati correlati alla crittografia archiviati con il BLOB. È assolutamente essenziale che qualsiasi meccanismo di copia assicuri che i metadati dei BLOB e, in particolare modo, i metadati correlati alla crittografia vengano conservati. Se si copiano i BLOB senza metadati, il contenuto dei BLOB non sarà più recuperabile. Per informazioni dettagliate sui metadati correlati alla crittografia, vedere [Crittografia lato client di Archiviazione di Azure](storage-client-side-encryption.md).

## Domande frequenti

1. **Gli account di archiviazione esistenti sono ancora disponibili?**

    Sì, gli account di archiviazione esistenti sono ancora disponibili e non hanno subito variazioni di prezzo o di funzionalità. Non possono scegliere un livello di accesso e in futuro non avranno funzionalità di suddivisione in livelli.

2. **Perché e quando iniziare a usare gli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB sono specializzati per l'archiviazione dei BLOB e consentono di introdurre nuove funzionalità incentrate sui BLOB. È consigliabile usare gli account di archiviazione BLOB per archiviare i BLOB perché in futuro verranno introdotte funzionalità, ad esempio l'archiviazione gerarchica e la suddivisione in livelli, basate su questo tipo di account. La scelta del momento per eseguire la migrazione dipende tuttavia dai requisiti aziendali dell'utente.

3. **Si può convertire un account di archiviazione esistente in un account di archiviazione BLOB?**

    No. L'account di archiviazione BLOB è un tipo diverso di account di archiviazione e sarà necessario crearne uno nuovo ed eseguire la migrazione dei dati come illustrato sopra.

4. **Si possono archiviare oggetti in entrambi i livelli di accesso nello stesso account?**

    L'attributo del livello di accesso viene impostato a livello di account e si applica a tutti gli oggetti in tale account. Non è possibile impostare il livello di accesso a livello di oggetto.

5. **Si può modificare il livello di accesso nell'account di archiviazione BLOB?**

    Sì. Sarà possibile modificare il livello di accesso nell'account di archiviazione. La modifica del livello di accesso a livello di account si applicherà a tutti gli oggetti archiviati nell'account. La modifica del livello di accesso da frequente a non frequente non comporta alcun addebito, mentre la modifica da non frequente a frequente comporta un costo per GB per la lettura di tutti i dati nell'account.

6. **Ogni quanto si può modificare il livello di accesso nell'account di archiviazione BLOB?**

    Anche se non esistono limiti alla frequenza con cui è possibile modificare il livello di accesso, tenere presente che la modifica del livello di accesso da non frequente a frequente comporta addebiti elevati. Non è consigliabile modificare frequentemente il livello di accesso.

7. **I BLOB nel livello di archiviazione ad accesso sporadico si comporteranno in modo diverso rispetto a quelli del livello di archiviazione ad accesso frequente?**

    I BLOB nel livello di archiviazione ad accesso frequente hanno la stessa latenza dei BLOB negli account di archiviazione di uso generico. I BLOB nel livello di archiviazione ad accesso sporadico hanno una latenza simile, in millisecondi, a quella dei BLOB negli account di archiviazione di uso generico.

    I BLOB nel livello di archiviazione ad accesso sporadico avranno una Contratto di servizio leggermente inferiore rispetto a quello dei BLOB nel livello di archiviazione ad accesso frequente. Per informazioni dettagliate, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage).

8. **È possibile archiviare i BLOB di pagine e i dischi delle macchine virtuali negli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine. Il backup dei dischi delle macchine virtuali di Azure viene eseguito dai BLOB di pagine. Non sarà quindi possibile usare gli account di archiviazione BLOB per archiviare i dischi delle macchine virtuali. È tuttavia possibile archiviare i backup dei dischi delle macchine virtuali come BLOB in blocchi in un account di archiviazione BLOB.

9. **Sarà necessario modificare le applicazioni esistenti per usare gli account di archiviazione BLOB?**

    Gli account di archiviazione BLOB offrono una coerenza API al 100% con gli account di archiviazione di uso generico per i BLOB in blocchi e i BLOB di aggiunta. Se l'applicazione usa BLOB in blocchi o i BLOB di aggiunta e si usa la versione 2014-02-14 dell'[API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) o versione successiva, l'applicazione dovrebbe funzionare correttamente. Se si usa una versione meno recente del protocollo, sarà necessario aggiornare l'applicazione per poter usare la nuova versione e lavorare quindi facilmente con entrambi i tipi di account di archiviazione. In generale, è sempre consigliabile usare la versione più recente indipendentemente dal tipo di account di archiviazione usato.

10. **L'esperienza utente sarà diversa?**

    Gli account di archiviazione BLOB sono molto simili agli account di archiviazione di uso generico per l'archiviazione di BLOB in blocchi o i BLOB di aggiunta e supportano tutte le funzionalità essenziali di Archiviazione di Azure, incluse durabilità e disponibilità elevate, scalabilità, prestazioni e sicurezza. Fatta eccezione per le funzionalità e le restrizioni specifiche degli account di archiviazione BLOB e per i livelli di accesso illustrati sopra, tutto il resto rimane invariato.

## Passaggi successivi

### Valutare gli account di archiviazione BLOB

[Verificare la disponibilità degli account di archiviazione BLOB in base all'area](https://azure.microsoft.com/regions/#services)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](storage-enable-and-view-metrics.md)

[Verificare i prezzi di Archiviazione di Azure per i BLOB in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)

### Iniziare a usare gli account di archiviazione BLOB

[Introduzione all'archivio BLOB di Azure](storage-dotnet-how-to-use-blobs.md)

[Spostamento dei dati da e verso Archiviazione di Azure](storage-moving-data.md)

[Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0511_2016-->