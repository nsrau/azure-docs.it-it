---
title: Rilevamento minacce - Istanza gestita di database SQL di Azure | Microsoft Docs
description: La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 2112a0a3997af478de6b8c80abcf7924a66302f0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Rilevamento delle minacce in Istanza gestita di database SQL di Azure

Rilevamento minacce di SQL rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database in un'istanza gestita di database SQL di Azure (anteprima).

## <a name="overview"></a>Panoramica

La funzionalità di rilevamento delle minacce individua le attività di database anomale che indicano la presenza di potenziali minacce alla sicurezza nell'istanza gestita. Rilevamento minacce è ora disponibile in anteprima per Istanza gestita.

Rilevamento minacce offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale del database. La funzionalità di rilevamento delle minacce rende più semplice affrontare le minacce potenziali per l'istanza gestita, senza dover essere esperti della sicurezza o gestire sistemi di controllo della sicurezza avanzati. Per un'esperienza di analisi completa, è consigliabile abilitare il controllo dell'istanza gestita di Azure, che scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure. 

Rilevamento minacce di SQL integra gli avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). Ogni istanza gestita protetta verrà fatturata allo stesso prezzo del livello Standard del Centro sicurezza di Azure, al prezzo di $ 15 per nodo al mese, dove ogni istanza gestita protetta viene conteggiata come un nodo.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurare Rilevamento minacce per l'istanza gestita tramite il portale di Azure
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione dell'istanza gestita che si vuole proteggere. Nella pagina **Impostazioni** selezionare **Rilevamento minacce**. 
3. Nella pagina di configurazione del rilevamento delle minacce 
   - Impostare il rilevamento delle minacce su **SÌ** .
   - Configurare l'**elenco di indirizzi di posta elettronica** che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
   - Selezionare l'**account di archiviazione di Azure** in cui vengono salvati i record di controllo delle minacce anomali. 
4.  Fare clic su **Salva** per salvare i criteri di rilevamento delle minacce nuovi o aggiornati.

   ![rilevamento delle minacce](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività anomale dell'istanza gestita quando viene rilevato un evento sospetto

1. Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. 

   Il messaggio di posta elettronica fornisce informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Fornisce anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia all'istanza gestita.

   ![messaggio di rilevamento di minacce](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Fare clic sul collegamento **Visualizza gli avvisi di SQL recenti** nel messaggio di posta elettronica per avviare il portale di Azure e visualizzare la pagina degli avvisi del Centro sicurezza di Azure, con una panoramica delle minacce SQL attive rilevate nel database dell'istanza gestita.

   ![minacce attive](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Fare clic su uno specifico avviso per visualizzare altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

   Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni basate su dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database. Negli avvisi di attacchi SQL injection, i dettagli includono l'istruzione SQL vulnerabile che ha subito un exploit.

   ![sql injection](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Avvisi di rilevamento delle minacce per l'istanza gestita 

Il servizio di rilevamento delle minacce per l'istanza gestita rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare i tipi di avvisi seguenti:
- **Vulnerabilità agli attacchi SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. Ciò potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Ci sono due possibili motivi per la generazione di un'istruzione non corretta:
 - Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
 - Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
- **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.
- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso a un'istanza gestita cambia, quando un utente ha effettuato l'accesso all'istanza gestita da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o un'operazione di manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente, un utente malintenzionato esterno e così via).
- **Accesso da un data center di Azure insolito**: questo avviso viene attivato quando il modello di accesso all'istanza gestita cambia, quando un utente ha effettuato l'accesso all'istanza gestita da un data center di Azure non rilevato durante l'accesso all'istanza gestita in un periodo recente. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione in Azure, Power BI, editor di query SQL di Azure e così via). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a un server dell'istanza gestita cambia, quando un utente ha effettuato l'accesso all'istanza gestita usando un'entità di sicurezza insolita (utente SQL). In alcuni casi, l'avviso rileva un'azione legittima (un'operazione di manutenzione di una nuova applicazione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Altre informazioni sul [controllo dell'istanza gestita](https://go.microsoft.com/fwlink/?linkid=869430) 
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
