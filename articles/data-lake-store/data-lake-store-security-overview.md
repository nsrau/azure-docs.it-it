<properties 
   pageTitle="Panoramica della sicurezza in Archivio Data Lake | Microsoft Azure" 
   description="Comprendere il motivo per cui Archivio Azure Data Lake è un archivio Big Data sicuro" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Sicurezza in Archivio Azure Data Lake

Un numero elevato di aziende si affida all'analisi di Big Data per ottenere una visione completa dell'attività e prendere decisioni appropriate. Queste organizzazioni possono presentare un ambiente complesso e regolamentato, con un numero crescente di utenti diversi. Per le aziende è quindi essenziale assicurarsi che i dati critici vengano archiviati in modo sicuro e con un livello di accesso adeguato concesso agli utenti interessati. Archivio Azure Data Lake è progettato tenendo presenti questi requisiti di sicurezza. Questo articolo fornisce informazioni sulle funzionalità di sicurezza di Archivio Azure Data Lake (ADLS), ad esempio:

* Autenticazione
* Authorization
* Isolamento rete
* Protezione dati
* Controllo

 
## Autenticazione e gestione delle identità

L'autenticazione è il processo mediante il quale gli utenti dimostrano la propria identità durante l'interazione con Archivio Data Lake o qualsiasi servizio che si collega ad Archivio Data Lake. Per la gestione delle identità e l'autenticazione, Archivio Data Lake usa [Azure Active Directory](../active-directory/active-directory-whatis.md) (AAD), una soluzione cloud globale per la gestione delle identità e dell'accesso che semplifica la gestione di utenti e gruppi.

Ogni sottoscrizione Azure può essere associata a un'istanza di Azure Active Directory. Solo gli utenti e le identità del servizio definite in questa directory possono accedere ad Archivio Data Lake tramite il portale di Azure, strumenti da riga di comando o applicazioni client compilate con Data Lake Store SDK. I vantaggi principali dell'uso di Azure Active Directory come meccanismo di controllo di accesso centralizzato sono:

* Semplifica la gestione del ciclo di vita dell'identità. L'identità di un utente o un servizio (identità principale del servizio) può essere creata e revocata in modo rapido semplicemente eliminando o disabilitando l'account nella directory.

* Supporta [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) che fornisce un ulteriore livello di sicurezza per l'accesso degli utenti e le transazioni.

* Consente agli utenti di eseguire l'autenticazione da qualsiasi client usando un protocollo aperto standard, ad esempio OAuth e OpenID.

* Consente la federazione con provider di identità cloud e servizi di directory dell'organizzazione.

## Autorizzazione e controllo di accesso

Dopo che un utente viene autenticato da AAD per accedere ad Archivio Azure Data Lake, l'autorizzazione controlla le autorizzazioni di accesso per Archivio Data Lake. Data Lake Store separa l'autorizzazione per le attività relative all'account e relative ai dati nel modo seguente.

* [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) (RBAC) fornito da Azure per la gestione degli account.
* POSIX ACL per l'accesso ai dati nell'archivio.

### Uso del controllo degli accessi in base al ruolo per la gestione degli account

Esistono quattro ruoli di base definiti per impostazione predefinita. Essi consentono di eseguire diverse operazioni su un account Archivio Data Lake tramite portale, cmdlet di PowerShell e API REST. I ruoli **Proprietario** e **Collaboratore** consentono un'ampia gamma di funzioni di amministrazione sull'account. Gli utenti limitati alla sola interazione con i dati possono essere aggiunti al ruolo **Lettore**.

![Ruoli Controllo degli accessi in base al ruolo](./media/data-lake-store-security-overview/rbac-roles.png "Ruoli Controllo degli accessi in base al ruolo")

Si noti che sebbene lo scopo dell'assegnazione di questi ruoli sia la gestione degli account, a seconda del ruolo specifico, potrebbe avere anche un'implicazione sulle autorizzazioni di accesso ai dati. Per le operazioni che l'utente può eseguire nel file system, sarà necessario usare gli elenchi di controllo di accesso (ACL). Di seguito è riportato un riepilogo dei diritti di gestione e dei diritti di accesso ai dati per questi ruoli.

| Ruoli | Diritti di gestione | Diritti di accesso ai dati | Spiegazione |
|--------------------------|---------------------------------|--------------------|-------------|
| Nessun ruolo assegnato | None | Regolato da ACL | In questi casi, gli utenti non possono usare il portale di Azure o i cmdlet di Azure PowerShell per esplorare Archivio Data Lake. Tali utenti dovranno fare affidamento solo su strumenti da riga di comando. |
| Proprietario | Tutti | Tutti | Il ruolo Proprietario si riferisce a un utente avanzato e consente quindi di gestire tutti gli elementi e disporre dell'accesso completo ai dati | 
| Lettore | Sola lettura | Regolato da ACL | Il ruolo Lettore consente di visualizzare tutti gli elementi riguardanti la gestione degli account, ad esempio l'utente assegnato a un determinato ruolo, ma non prevede la possibilità di apportare modifiche |
| Collaboratore | Tutti tranne quelli di aggiunta e rimozione dei ruoli | Regolato da ACL | Il ruolo Collaboratore consente di gestire altri aspetti di un account, ad esempio la creazione/gestione di avvisi, la distribuzione e così via. Un collaboratore non può aggiungere o rimuovere ruoli |
| Amministratore Accesso utenti | Aggiunta e rimozione dei ruoli | Regolato da ACL | Il ruolo Amministratore Accesso utenti consente di gestire l'accesso degli utenti all'account. |

Per istruzioni, vedere [Assegnare utenti o gruppi di sicurezza ad account di Archivio Data Lake di Azure](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Uso degli ACL per le operazioni sui file system

Azure Data Lake Store è un file system gerarchico come HDFS e supporta [ACL POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists), consentendo diritti di accesso in lettura (r), scrittura (w) ed esecuzione (x) a risorse concesse a proprietario, gruppo proprietario e altri utenti/gruppi. Nella versione di anteprima pubblica di Data Lake Store (versione corrente) gli ACL possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Gli elenchi di controllo di accesso applicati alla cartella radice saranno applicabili anche a ogni cartella e file figlio.

È consigliabile definire gli ACL per molti utenti usando [gruppi di sicurezza](../active-directory/active-directory-accessmanagement-manage-groups.md). Raggruppare gli utenti in un gruppo di sicurezza, quindi assegnare gli ACL per il file e la cartella al gruppo di sicurezza. Ciò è utile quando si fornisce accesso personalizzato perché è previsto un limite in cui è possibile aggiungere solo un massimo di nove voci come parte dell'accesso stesso. Vedere [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Archivio di Azure Data Lake](data-lake-store-secure-data.md#filepermissions) per altre informazioni sulla protezione dei dati presenti in Data Lake Store usando gruppi di sicurezza AAD.

![Elencare profili di accesso standard e personalizzato](./media/data-lake-store-security-overview/adl.acl.2.png "Elencare profili di accesso standard e personalizzato")

## Isolamento rete

Archivio Azure Data Lake consente di bloccare ulteriormente l'accesso all'archivio dati a livello di rete. È possibile abilitare il firewall e definire un intervallo di indirizzi IP per i client attendibili. Dopo l'abilitazione, solo i client con indirizzo IP compreso nell'intervallo definito possono connettersi all'archivio.

![Impostazioni del firewall e accesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Impostazioni del firewall e indirizzo IP")

## Protezione dati

Le organizzazioni desiderano assicurarsi che i dati aziendali critici siano protetti durante l'intero ciclo di vita. Per i dati in transito, Archivio Data Lake usa TLS standard di settore (protocollo Transport Layer Security) per proteggere i dati tra client e sicurezza Data Lake.

Nelle versioni future sarà disponibile anche la protezione dei dati inattivi.

## Log di controllo e diagnostica

È possibile usare i log di diagnostica o di controllo a seconda se si stanno cercando log relativi alle attività correlate alla gestione o alle attività correlate ai dati.

*  Le attività correlate alla gestione usano le API di Azure Resource Manager e vengono rilevate nel portale di Azure tramite i log di controllo.
*  Le attività correlate ai dati usano le API WebHDFS e vengono rilevate nel portale di Azure tramite i log di diagnostica.

### Log di controllo

Per conformità alle normative, le organizzazioni potrebbero richiedere audit trail adeguati nel caso fosse necessario approfondire un evento imprevisto. Archivio Data Lake prevede monitoraggio e controllo integrati dove registra tutte le attività di gestione dell'account.

Per gli audit trail per la gestione degli account, è possibile visualizzare e scegliere le colonne di interesse per la registrazione e l'esportazione dei log di controllo nell'archivio di Azure.

![Log di controllo](./media/data-lake-store-security-overview/audit-logs.png "Log di controllo")

### Log di diagnostica

È possibile abilitare gli audit trail dell'accesso ai dati dal portale di Azure (**Impostazioni di diagnostica**) e specificare un account di archiviazione BLOB di Azure in cui verranno archiviati i log.

![Log di diagnostica](./media/data-lake-store-security-overview/diagnostic-logs.png "Log di diagnostica")

Dopo aver attivato le impostazioni di diagnostica, è possibile controllare i log nella scheda **Log di diagnostica**.

Per altre informazioni sull'uso dei log di diagnostica con Azure Data Lake Store, vedere [Accesso ai log di diagnostica per Azure Data Lake Store](data-lake-store-diagnostic-logs.md).

## Riepilogo

I clienti aziendali richiedono una piattaforma cloud di analisi dei dati protetta e facile da usare. Archivio Azure Data Lake è stato progettato per soddisfare questi requisiti con la gestione delle identità e l'autenticazione tramite l'integrazione di Azure Active Directory, l'autorizzazione basata su ALC, l'isolamento della rete, la crittografia dei dati in transito e inattivi (disponibile in futuro) e il controllo.

Se si vogliono vedere nuove funzionalità incluse in Data Lake Store, inviare commenti e suggerimenti al [forum Uservoice](https://feedback.azure.com/forums/327234-data-lake).

## Vedere anche

- [Panoramica dell’Archivio Data Lake di Azure](data-lake-store-overview.md)
- [Introduzione ad Archivio Data Lake](data-lake-store-get-started-portal.md)
- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0803_2016-->