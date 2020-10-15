---
title: Modelli di crittografia dei dati in Microsoft Azure
description: Questo articolo fornisce una panoramica dei modelli di crittografia dei dati in Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 7b92c84234432320aa08017a15fbf8a5a4630eb3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019733"
---
# <a name="data-encryption-models"></a>Modelli di crittografia dei dati

Comprendere i diversi modelli di crittografia, e i relativi vantaggi e svantaggi, è fondamentale per capire come i vari provider di risorse in Azure implementano la crittografia dei dati inattivi. Queste definizioni sono condivise tra tutti i provider di risorse in Azure per garantire un linguaggio e una tassonomia comuni.

Esistono tre scenari per la crittografia sul lato server:

- Crittografia sul lato server con chiavi gestite dal servizio
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Microsoft gestisce le chiavi
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi tramite Azure Key Vault
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente su hardware controllato dal cliente
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi su hardware controllato dal cliente
  - Funzionalità cloud complete

I modelli di crittografia lato server fanno riferimento alla crittografia che viene eseguita dal servizio di Azure. In questo modello, le operazioni di crittografia e decrittografia sono eseguite dal provider di risorse. Archiviazione di Azure può ad esempio ricevere i dati in operazioni di testo normale ed esegue internamente la crittografia e la decrittografia. Il provider di risorse può usare chiavi di crittografia gestite da Microsoft o dal cliente, a seconda della configurazione specificata.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Ognuno dei modelli di crittografia dei dati inattivi lato server implica caratteristiche distintive per la gestione delle chiavi. Questo include le posizioni e le modalità di creazione e archiviazione delle chiavi di crittografia, nonché i modelli di accesso e le procedure di rotazione delle chiavi.  

Per la crittografia lato client, considerare quanto segue:

- I servizi di Azure non possono visualizzare i dati decrittografati
- I clienti mantengono e archiviano le chiavi in locale o in altri archivi sicuri. Le chiavi non sono disponibili per i servizi di Azure
- Funzionalità cloud ridotte

I modelli di crittografia supportati in Azure sono suddivisi in due gruppi principali: "crittografia client" e "crittografia lato server", come indicato in precedenza. Indipendentemente dal modello di crittografia dei dati inattivi in uso, per i servizi di Azure è sempre consigliabile usare un trasporto protetto, ad esempio TLS o HTTPS. La crittografia a livello di trasporto deve quindi essere gestita dal protocollo di trasporto e non deve rappresentare un fattore determinante per la scelta del modello di crittografia dei dati inattivi da usare.

## <a name="client-encryption-model"></a>Modello di crittografia client

Il modello di crittografia client fa riferimento alla crittografia che viene eseguita all'esterno del provider di risorse o da Azure dal servizio o dall'applicazione chiamante. La crittografia può essere eseguita dall'applicazione del servizio in Azure o da un'applicazione in esecuzione nel data center del cliente. In entrambi i casi, quando si usa questo modello di crittografia, il provider di risorse di Azure riceve un blob di dati crittografato senza alcuna possibilità di decrittografare i dati o avere accesso alle chiavi di crittografia. In questo modello la gestione delle chiavi viene eseguita dal servizio o dall'applicazione chiamante ed è opaca al servizio di Azure.

![Client](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Crittografia lato server con chiavi gestite dal servizio

Per molti clienti, il requisito essenziale consiste nel garantire che i dati siano crittografati ogni volta che sono inattivi. La crittografia sul lato server tramite le chiavi gestite dal servizio rende possibile questo modello, consentendo ai clienti di contrassegnare le specifiche risorse (account di archiviazione, database SQL e così via) per la crittografia e lasciare a Microsoft tutti gli aspetti di gestione delle chiavi, come il rilascio, la rotazione e il backup. La maggior parte dei servizi di Azure che supportano la crittografia inattiva in genere supporta questo modello di offload della gestione delle chiavi di crittografia in Azure. Il provider di risorse di Azure crea le chiavi, le inserisce in un archivio protetto e le recupera quando necessario. Questo significa che il servizio ha l'accesso completo alle chiavi e il pieno controllo della gestione del ciclo di vita delle credenziali.

![gestito](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

La crittografia sul lato server con chiavi gestite dal servizio consente pertanto di soddisfare rapidamente l'esigenza di implementare la crittografia dei dati inattivi con un sovraccarico limitato per il cliente. Quando disponibile, un cliente apre il portale di Azure per la sottoscrizione e il provider di risorse di destinazione e seleziona una casella per indicare che vuole che i dati vengano crittografati. In alcuni manager delle risorse la crittografia lato server con chiavi gestite dal servizio è attiva per impostazione predefinita.

La crittografia sul lato server con chiavi gestite da Microsoft implica che il servizio ha accesso completo all'archiviazione e gestisce le chiavi. Anche se alcuni clienti potrebbero voler gestire le chiavi perché pensano di garantire una maggiore sicurezza, durante la valutazione di questo modello è importante tenere conto del costo e del rischio associato a una soluzione personalizzata di archiviazione delle chiavi. In molti casi, un'organizzazione può stabilire che i vincoli di risorse o i rischi di una soluzione locale potrebbero essere maggiori rispetto al rischio associato alla gestione nel cloud delle chiavi di crittografia dei dati inattivi.  Tuttavia, questo modello potrebbe non essere sufficiente per le organizzazioni che hanno requisiti per controllare la creazione o il ciclo di vita delle chiavi di crittografia o per avere un personale diverso per gestire le chiavi di crittografia di un servizio rispetto a quelle che gestiscono il servizio, ovvero la separazione della gestione delle chiavi dal modello di gestione generale per il servizio.

### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia sul lato server con chiavi gestite dal servizio, la creazione delle chiavi, l'archiviazione e l'accesso al servizio sono gestiti dal servizio. In genere, i principali provider di risorse di Azure archiviano le chiavi DEK in un archivio vicino ai dati e rapidamente disponibile e accessibile, mentre le chiavi KEK sono archiviate in un archivio interno protetto.

**Vantaggi**

- Configurazione semplice
- Microsoft gestisce la rotazione, il backup e la ridondanza delle chiavi
- Il cliente non deve sostenere il costo associato all'implementazione o il rischio di uno schema di gestione delle chiavi personalizzato.

**Svantaggi**

- Nessun controllo del cliente sulle chiavi di crittografia (specifica della chiave, ciclo di vita, revoca e così via)
- Nessuna possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault

Per gli scenari in cui il requisito prevede di crittografare i dati inattivi e controllare le chiavi di crittografia, i clienti possono usare la crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault. Alcuni servizi possono archiviare solo la chiave KEK radice in Azure Key Vault e archiviano la chiave DEK crittografata in un percorso interno più vicino ai dati. In questo scenario, i clienti possono usare le proprie chiavi nell'insieme di credenziali delle chiavi (BYOK, Bring Your Own Key) o generare nuove chiavi e usarle per crittografare le risorse desiderate. Mentre il provider di risorse esegue le operazioni di crittografia e decrittografia, usa la chiave di crittografia della chiave configurata come chiave radice per tutte le operazioni di crittografia.

La perdita di chiavi di crittografia delle chiavi comporta la perdita di dati. Per questo motivo, le chiavi non devono essere eliminate. È necessario eseguire il backup delle chiavi ogni volta che viene creato o ruotato. L' [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea deve essere abilitata in qualsiasi insieme di credenziali che archivia le chiavi di crittografia della chiave. Anziché eliminare una chiave, impostare Enabled su false o impostare la data di scadenza.

### <a name="key-access"></a>Accesso alle chiavi

Il modello di crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault prevede che il servizio di accesso alle chiavi possa eseguire la crittografia e la decrittografia in base alle esigenze. Le chiavi per la crittografia dei dati inattivi vengono rese accessibili a un servizio tramite un criterio di controllo di accesso che concede all'identità del servizio l'accesso per ricevere la chiave. Un servizio di Azure in esecuzione per conto di una sottoscrizione associata può essere configurato con un'identità all'interno della sottoscrizione. Il servizio può eseguire l'autenticazione di Azure Active Directory e ricevere un token di autenticazione che lo identifica come un servizio che opera per conto della sottoscrizione. Il token può quindi essere presentato all'insieme di credenziali delle chiavi per ottenere una chiave a cui è stato consentito l'accesso.

Per le operazioni con chiavi di crittografia, può essere concesso l'accesso a un'identità del servizio per qualsiasi delle operazioni seguenti: decrypt, encrypt, unwrapkey, wrapkey, verify, sign, get, list, update, create, import, delete, backup e restore.

Per ottenere una chiave da usare per la crittografia o la decrittografia dei dati inattivi, l'identità del servizio con cui verrà eseguita l'istanza del servizio Resource Manager deve disporre di UnwrapKey (per ottenere la chiave per la decrittografia) e WrapKey (per inserire una chiave nell'insieme di credenziali delle chiavi al momento della creazione di una nuova chiave).

>[!NOTE]
>Per altri dettagli sull'autorizzazione dell'insieme di credenziali delle chiavi, vedere la pagina Proteggere l'insieme di credenziali delle chiavi nella [documentazione di Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Vantaggi**

- Controllo completo sulle chiavi usate: le chiavi di crittografia vengono gestite nel Key Vault del cliente sotto il controllo del cliente.
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Il cliente ha la piena responsabilità per la gestione dell'accesso alle chiavi
- Il cliente ha la piena responsabilità per la gestione del ciclo di vita delle chiavi
- Sovraccarico aggiuntivo per l'installazione e la configurazione

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Crittografia lato server con chiavi gestite dal cliente nell'hardware controllato dal cliente

Alcuni servizi di Azure consentono il modello di gestione delle chiavi HYOK (Host Your Own Key). Questa modalità di gestione è utile negli scenari in cui è necessario crittografare i dati inattivi e gestire le chiavi in un repository proprietario al di fuori del controllo di Microsoft. In questo modello il servizio deve recuperare la chiave da un sito esterno. Ciò ha effetto sulle garanzie di prestazioni e disponibilità e la configurazione risulta più complessa. Inoltre, poiché il servizio non ha accesso alla chiave DEK durante le operazioni di crittografia e decrittografia, le garanzie di sicurezza complessiva di questo modello sono simili allo scenario in cui le chiavi sono gestite dal cliente in Azure Key Vault.  Di conseguenza, questo modello non è appropriato per la maggior parte delle organizzazioni, a meno che non siano presenti specifici requisiti di gestione delle chiavi. A causa di queste limitazioni, la maggior parte dei servizi di Azure non supporta la crittografia lato server con chiavi gestite dal server nell'hardware controllato dai clienti.

### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia lato server con chiavi gestite dal servizio nell'hardware controllato dal cliente, le chiavi vengono mantenute in un sistema configurato dal cliente. I servizi di Azure che supportano questo modello forniscono un sistema per stabilire una connessione sicura a un archivio delle chiavi fornito dal cliente.

**Vantaggi**

- Controllo completo della chiave radice in uso: le chiavi di crittografia vengono gestite tramite un archivio fornito dal cliente
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Piena responsabilità per l'archiviazione di chiavi, sicurezza, prestazioni e disponibilità
- Piena responsabilità per la gestione dell'accesso alle chiavi
- Piena responsabilità per la gestione del ciclo di vita delle chiavi
- Significativi costi di installazione, configurazione e manutenzione
- Maggiore dipendenza dalla disponibilità della rete tra il data center del cliente e i data center di Azure.

## <a name="supporting-services"></a>Servizi di supporto
I servizi di Azure che supportano ogni modello di crittografia:

| Prodotto, funzionalità o servizio | Lato server con chiave gestita dal servizio   | Server-Side uso della chiave Customer-Managed | Client-Side uso della chiave Client-Managed  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **Intelligenza artificiale e Machine Learning**      |                    |                    |                    |
| Ricerca cognitiva di Azure           | Sì                | Sì                | -                  |
| Servizi cognitivi di Azure         | Sì                | Sì                | -                  |
| Azure Machine Learning           | Sì                | Sì                | -                  |
| Azure Machine Learning Studio (versione classica) | Sì         | Anteprima, RSA a 2048 bit | -               |
| Content Moderator                | Sì                | Sì                | -                  |
| Viso                             | Sì                | Sì                | -                  |
| Language Understanding           | Sì                | Sì                | -                  |
| Personalizza esperienze                     | Sì                | Sì                | -                  |
| QnA Maker                        | Sì                | Sì                | -                  |
| Servizi Voce                  | Sì                | Sì                | -                  |
| Traduzione testuale                  | Sì                | Sì                | -                  |
| Power BI                         | Sì                | Sì, RSA 4096 bit  | -                  |
| **Analisi**                    |                    |                    |                    |
| Analisi di flusso di Azure           | Sì                | N/D\*              | -                  |
| Hub eventi                       | Sì                | Sì                | -                  |
| Funzioni                        | Sì                | Sì                | -                  |
| Azure Analysis Services          | Sì                | -                  | -                  |
| Azure Data Catalog               | Sì                | -                  | -                  |
| Azure HDInsight                  | Sì                | All                | -                  |
| Application Insights di monitoraggio di Azure | Sì                | Sì                | -                  |
| Log Analytics di monitoraggio di Azure      | Sì                | Sì                | -                  |
| Esplora dati di Azure              | Sì                | Sì                | -                  |
| Azure Data Factory               | Sì                | Sì                | -                  |
| Archivio Azure Data Lake            | Sì                | Sì, RSA a 2048 bit  | -                  |
| **Contenitori**                   |                    |                    |                    |
| Servizio Azure Kubernetes         | Sì                | Sì                | -                  |
| Istanze di Container              | Sì                | Sì                | -                  |
| Registro Container               | Sì                | Sì                | -                  |
| **Compute**                      |                    |                    |                    |
| Macchine virtuali                 | Sì                | Sì                | -                  |
| Set di scalabilità di macchine virtuali        | Sì                | Sì                | -                  |
| SAP HANA                         | Sì                | Sì                | -                  |
| Servizio app                      | Sì                | Sì\*\*            | -                  |
| Automazione                       | Sì                | Sì\*\*            | -                  |
| Funzioni di Azure                  | Sì                | Sì\*\*            | -                  |
| Portale di Azure                     | Sì                | Sì\*\*            | -                  |
| App per la logica                       | Sì                | Sì                | -                  |
| Applicazioni gestite da Azure       | Sì                | Sì\*\*            | -                  |
| Bus di servizio                      | Sì                | Sì                | -                  |
| Site Recovery                    | Sì                | Sì                | -                  |
| **Database**                    |                    |                    |                    |
| SQL Server in macchine virtuali   | Sì                | Sì                | Sì                |
| database SQL di Azure               | Sì                | Sì, RSA 3072 bit  | Sì                |
| Database SQL di Azure per MariaDB   | Sì                | -                  | -                  |
| Database SQL di Azure per MySQL     | Sì                | Sì                | -                  |
| Database SQL di Azure per PostgreSQL | Sì               | Sì                | -                  |
| Azure Synapse Analytics          | Sì                | Sì, RSA 3072 bit  | -                  |
| SQL Server Stretch Database      | Sì                | Sì, RSA 3072 bit  | Sì                |
| Archiviazione tabelle                    | Sì                | Sì                | Sì                |
| Azure Cosmos DB                  | Sì                | Sì                | -                  |
| Azure Databricks                 | Sì                | Sì                | -                  |
| Servizio Migrazione del database di Azure | Sì                | N/D\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Sì                | -                  | Sì                |
| Azure Repos                      | Sì                | -                  | Sì                |
| **Identità**                     |                    |                    |                    |
| Azure Active Directory           | Sì                | -                  | -                  |
| Servizi di dominio Azure Active Directory | Sì          | Sì                | -                  |
| **Integrazione**                  |                    |                    |                    |
| Bus di servizio                      | Sì                | Sì                | Sì                |
| Griglia di eventi                       | Sì                | -                  | -                  |
| Gestione API                   | Sì                | -                  | -                  |
| **Servizi IoT**                 |                    |                    |                    |
| Hub IoT                          | Sì                | Sì                | Sì                |
| Provisioning dei dispositivi dell'hub Internet      | Sì                | Sì                | -                  |
| **Gestione e governance**    |                    |                    |                    |
| Azure Site Recovery              | Sì                | -                  | -                  |
| Azure Migrate                    | Sì                | Sì                | -                  |
| **Supporti**                        |                    |                    |                    |
| Servizi multimediali                   | Sì                | -                  | Sì                |
| **Sicurezza**                     |                    |                    |                    |
| Centro sicurezza di Azure per IoT    | Sì                | Sì                | -                  |
| Azure Sentinel                   | Sì                | Sì                | -                  |
| **Storage**                      |                    |                    |                    |
| Archiviazione BLOB                     | Sì                | Sì                | Sì                |
| Archiviazione BLOB Premium             | Sì                | Sì                | Sì                |
| Archiviazione su disco                     | Sì                | Sì                | -                  |
| archiviazione su disco Ultra               | Sì                | Sì                | -                  |
| archiviazione su disco gestite             | Sì                | Sì                | -                  |
| Archiviazione file                     | Sì                | Sì                | -                  |
| Archiviazione Premium file             | Sì                | Sì                | -                  |
| Sincronizzazione file                        | Sì                | Sì                | -                  |
| Archiviazione code                    | Sì                | Sì                | Sì                |
| Avere vFXT                       | Sì                | -                  | -                  |
| Cache Redis di Azure            | Sì                | N/D\*              | -                  |
| Azure NetApp Files               | Sì                | Sì                | -                  |
| Spazio di archiviazione                  | Sì                | Sì                | -                  |
| StorSimple                       | Sì                | Sì                | Sì                |
| Backup di Azure                     | Sì                | Sì                | Sì                |
| Data Box                         | Sì                | -                  | Sì                |
| Data Box Edge                    | Sì                | Sì                | -                  |

\* Questo servizio non rende persistenti i dati. Le cache temporanee, se presenti, vengono crittografate con una chiave Microsoft.

\*\* Questo servizio supporta l'archiviazione dei dati nel Key Vault, nell'account di archiviazione o in un altro servizio di mantenimento dei dati che supporta già la crittografia Server-Side con Customer-Managed chiave.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come viene usata la crittografia in Azure](encryption-overview.md).
- Informazioni su come Azure usa la [crittografia doppia](double-encryption.md) per attenuare le minacce che derivano dalla crittografia dei dati.
