---
title: Usare Data Box e altri metodi per l'inserimento di dati offline in Sincronizzazione file di Azure.
description: Processo e procedure consigliate per abilitare il supporto della migrazione in blocco compatibile con la sincronizzazione.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212666"
---
# <a name="migrate-to-azure-file-sync"></a>Eseguire la migrazione a Sincronizzazione file di Azure
Sono disponibili diverse opzioni per lo spostamento in Sincronizzazione file di Azure:

### <a name="uploading-files-via-azure-file-sync"></a>Caricamento di file tramite Sincronizzazione file di Azure
L'opzione più semplice è spostare i file in locale in un computer Windows Server 2012 R2 o versione successiva e installare l'agente di Sincronizzazione file di Azure. Una volta configurata la sincronizzazione, i file verranno caricati dal server. Attualmente si registra una velocità di caricamento media tra tutti i clienti di 1 TB ogni 2 giorni.
Per assicurarsi che il server non causi problemi nel data center, considerare l'uso di una [pianificazione per la limitazione della larghezza di banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).

### <a name="offline-bulk-transfer"></a>Trasferimento in blocco offline
Anche se il caricamento è senza dubbio l'opzione più semplice, potrebbe non funzionare se la larghezza di banda disponibile non consente di sincronizzare i file in Azure in un intervallo di tempo ragionevole. La sfida da affrontare è la sincronizzazione iniziale dell'intero set di file. Successivamente, Sincronizzazione file di Azure sposterà le modifiche solo quando si verificano nello spazio dei nomi, il che in genere usa molto meno larghezza di banda.
Per risolvere la sfida del caricamento iniziale si possono usare strumenti di migrazione in blocco offline, ad esempio la [famiglia di prodotti Azure Data Box](https://azure.microsoft.com/services/storage/databox). L'articolo seguente illustra il processo da seguire per usare la migrazione offline in modo compatibile con Sincronizzazione file di Azure. Descrive le procedure consigliate per evitare la creazione di file in conflitto e preservare i timestamp e gli ACL di file e cartelle dopo aver abilitato la sincronizzazione.

### <a name="online-migration-tools"></a>Strumenti di migrazione online
Il processo descritto di seguito non funziona solo per Data Box. Funziona per qualsiasi strumento di migrazione offline, come Data Box, o con strumenti online, ad esempio AzCopy, Robocopy o strumenti e servizi di terze parti. Pertanto, indipendentemente dal metodo usato per risolvere il problema del caricamento iniziale, è comunque importante seguire i passaggi descritti di seguito per utilizzare questi strumenti in modo compatibile con la sincronizzazione.


## <a name="offline-data-transfer-benefits"></a>Vantaggi del trasferimento dei dati offline
Questi sono i principali vantaggi della migrazione offline quando si usa un Data Box:

- Quando si esegue la migrazione di file in Azure tramite un processo di trasferimento in blocco offline, ad esempio Data Box, non è necessario caricare tutti i file dal server tramite la rete. Per gli spazi dei nomi di grandi dimensioni, questo può tradursi in risparmi significativi in termini di tempo e larghezza di banda di rete.
- Quando si usa Sincronizzazione file di Azure, indipendentemente dalla modalità di trasporto usata, ad esempio importazione di Azure, Data Box e così via, il server attivo carica solo i file che sono stati modificati dopo la spedizione dei dati ad Azure.
- Sincronizzazione file di Azure assicura che vengano sincronizzati anche gli ACL di file e cartelle, anche se il prodotto di migrazione in blocco offline non trasporta gli ACL.
- Quando si usano Azure Data Box e Sincronizzazione file di Azure, il tempo di inattività è pari a zero. L'uso di Data Box per trasferire i dati in Azure consente un utilizzo efficiente della larghezza di banda di rete, preservando allo stesso tempo la fedeltà dei file. Consente anche di mantenere aggiornato lo spazio dei nomi, caricando solo i file modificati dopo l'invio del dispositivo Data Box.

## <a name="plan-your-offline-data-transfer"></a>Pianificare il trasferimento dei dati offline
Prima di iniziare, esaminare le informazioni seguenti:

- Completare la migrazione in blocco verso una o più condivisioni file di Azure prima di abilitare la sincronizzazione con Sincronizzazione file di Azure.
- Se si prevede di usare Data Box per la migrazione in blocco: Esaminare i [prerequisiti della distribuzione per Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Pianificazione della topologia finale di Sincronizzazione file di Azure: [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- Selezionare gli account di archiviazione di Azure che conterranno le condivisioni file con cui si vuole eseguire la sincronizzazione. Assicurarsi che la migrazione in blocco venga eseguita in condivisioni di staging temporanee negli stessi account di archiviazione. La migrazione in blocco può essere abilitata solo usando una condivisione finale e una condivisione di staging che si trovano nello stesso account di archiviazione.
- Una migrazione in blocco può essere usata solo quando si crea una nuova relazione di sincronizzazione con un percorso server. Non è possibile abilitare una migrazione in blocco con una relazione di sincronizzazione esistente.

## <a name="offline-data-transfer-process"></a>Processo di trasferimento dei dati offline
Questa sezione descrive come configurare Sincronizzazione file di Azure in modo compatibile con strumenti di migrazione in blocco come Azure Data Box.

![Passaggi del processo, anche illustrati in dettaglio in un paragrafo seguente](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passaggio | Dettagli |
|---|---------------------------------------------------------------------------------------|
| ![Passaggio 1 del processo](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Ordinare il Data Box](../../databox/data-box-deploy-ordered.md). Esistono [varie offerte all'interno della famiglia di prodotti Data Box](https://azure.microsoft.com/services/storage/databox/data), adatte alle varie esigenze. Una volta ricevuto il Data Box, seguire la [documentazione del Data Box per copiare i dati](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Assicurarsi che i dati vengono copiati in questo percorso UNC del Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>`, dove `ShareName` è il nome della condivisione di staging. Inviare il Data Box ad Azure. |
| ![Passaggio 2 del processo](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendere finché i file non compaiono nelle condivisioni file di Azure designate come condivisioni di staging temporanee. **Non abilitare la sincronizzazione in queste condivisioni.** |
| ![Passaggio 3 del processo](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Creare una nuova condivisione vuota per ogni condivisione file che Data Box ha creato automaticamente. Assicurarsi che questa nuova condivisione sia nello stesso account di archiviazione della condivisione del Data Box. [Come creare una nuova condivisione file di Azure](storage-how-to-create-file-share.md). |
| ![Passaggio 4 del processo](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Creare un gruppo di sincronizzazione](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in un servizio di sincronizzazione archiviazione e indicare la condivisione vuota come endpoint cloud. Ripetere questo passaggio per ogni condivisione file del Data Box. Esaminare la guida [Distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md) e seguire i passaggi necessari per configurare Sincronizzazione file di Azure. |
| ![Passaggio 5 del processo](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Aggiungere la directory del server live come endpoint server](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Specificare nel processo che i file sono già stati spostati in Azure e fare riferimento alle condivisioni di staging. Si può scegliere di abilitare o disabilitare il cloud a livelli in base alle esigenze. Durante la creazione di un endpoint server nel server live, è necessario fare riferimento alla condivisione di staging. Abilitare "Trasferimento dati offline" (immagine seguente) nel pannello del nuovo endpoint server e fare riferimento alla condivisione di staging che deve risiedere nello stesso account di archiviazione dell'endpoint cloud. L'elenco delle condivisioni disponibili viene filtrato per account di archiviazione e condivisioni che non sono già in sincronizzazione. |

![Interfaccia utente del portale di Azure per abilitare il trasferimento dei dati offline durante la creazione di un nuovo endpoint server.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizzazione della condivisione
Dopo aver creato l'endpoint server, la sincronizzazione verrà avviata. Per ogni file presente nel server, la sincronizzazione determinerà se questo file è presente anche nella condivisione di staging in cui Data Box ha depositato i file e, in tal caso, copierà il file dalla condivisione di staging anziché caricarlo dal server. Se il file non esiste nella condivisione di staging o se nel server locale è disponibile una versione più recente, la sincronizzazione caricherà il file dal server locale.

> [!IMPORTANT]
> È possibile abilitare la modalità di migrazione in blocco solo durante la creazione di un endpoint server. Dopo aver creato un endpoint server, non è attualmente possibile integrare i dati migrati in blocco da un server già in sincronizzazione nello spazio dei nomi.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL e timestamp di file e cartelle
Sincronizzazione file di Azure assicura che gli ACL di file e cartelle vengano sincronizzati dal server live anche se lo strumento di migrazione in blocco usato inizialmente non trasportava gli ACL. Ciò significa che non c'è problema se la condivisione di staging non contiene alcun ACL per file e cartelle. Quando si abilita la funzionalità di migrazione dei dati offline durante la creazione di un nuovo endpoint server, gli ACL verranno sincronizzati per tutti i file nel server. Lo stesso vale per i timestamp di creazione e modifica.

## <a name="shape-of-the-namespace"></a>Forma dello spazio dei nomi
La forma dello spazio dei nomi è determinata dal contenuto del server nel momento in cui viene abilitata la sincronizzazione. Se dei file vengono eliminati nel server locale dopo l'operazione di snapshot e migrazione del Data Box, tali file non saranno trasferiti nello spazio dei nomi di sincronizzazione live. Saranno ancora nella condivisione di staging, ma non verranno mai copiati. Questo è il comportamento desiderato, perché la sincronizzazione mantiene lo spazio dei nomi in base al server live. Lo "snapshot" del Data Box è semplicemente un luogo di gestione temporanea per la copia efficiente dei file e non l'autorità per la forma dello spazio dei nomi live.

## <a name="finishing-bulk-migration-and-clean-up"></a>Completamento della migrazione in blocco e pulizia
Lo screenshot seguente mostra il pannello delle proprietà dell'endpoint server nel portale di Azure. Nella sezione relativa al trasferimento dei dati offline, è possibile osservare lo stato del processo. Mostrerà "In corso" o "Completato".

Dopo che il server ha completato la sincronizzazione iniziale dell'intero spazio dei nomi, avrà terminato l'utilizzo della condivisione file di staging con i file migrati in blocco da Data Box. Osservare nelle proprietà dell'endpoint server per il trasferimento dei dati offline che lo stato cambia in "Completato". A questo punto, è possibile pulire la condivisione di staging per risparmiare sui costi:

1. Selezionare "Disabilita trasferimento dati offline" nelle proprietà dell'endpoint server quando lo stato è "Completato".
2. Valutare l'eliminazione della condivisione di staging per risparmiare sui costi. La condivisione di staging probabilmente non conterrà ACL di file e cartelle, pertanto ha un utilizzo limitato. A scopi di backup temporizzato, creare invece un vero [snapshot della condivisione file di Azure in sincronizzazione](storage-snapshots-files.md). È possibile [abilitare Backup di Azure per l'acquisizione di snapshot]( ../../backup/backup-azure-files.md) in base a una pianificazione.

![Interfaccia utente del portale di Azure per le proprietà dell'endpoint server, in cui si trovano lo stato e i controlli di disabilitazione per il trasferimento dei dati offline.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

È consigliabile disabilitare questa modalità solo quando lo stato è "Completato" o si vuole realmente interrompere l'operazione a causa di un errore di configurazione. Se si disabilita la modalità nel corso di una distribuzione legittima, inizierà il caricamento dei file dal server, anche se la condivisione di staging è ancora disponibile.

> [!IMPORTANT]
> Dopo aver disabilitato il trasferimento dei dati offline non è possibile abilitarlo nuovamente, anche se la condivisione di staging usata per la migrazione in blocco è ancora disponibile.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
