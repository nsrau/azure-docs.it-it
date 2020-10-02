---
title: Gestione dei lead nel marketplace commerciale Microsoft
description: Informazioni sulla generazione e la ricezione di lead dei clienti provenienti dalle offerte Microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 5627d6afab22defebeebd3f04695ef95390f1163
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653316"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Lead di clienti tramite l'offerta del marketplace commerciale

I lead sono i clienti interessati a distribuire le offerte da [Microsoft AppSource](https://appsource.microsoft.com) e [Azure Marketplace](https://azuremarketplace.microsoft.com). È possibile ricevere i lead dei clienti dopo la pubblicazione dell'offerta nel marketplace commerciale. In questo articolo vengono descritti i concetti di gestione dei lead seguenti:

* il modo in cui l'offerta del marketplace commerciale genera lead dei clienti per assicurarsi di non perdere opportunità aziendali; 
* come connettere il sistema CRM (Customer Relationship Management) all'offerta per poter gestire i lead in un'unica posizione centrale;
* i dati dei lead inviati da Microsoft per poter seguire i clienti da cui si è stati contattati.

## <a name="generate-customer-leads"></a>Generare lead clienti

Di seguito sono riportati i punti in cui viene generato un lead.

- Un cliente acconsente a condividere le sue informazioni dopo aver selezionato **Contatta** dal marketplace commerciale. Questo lead è un lead di *interesse iniziale*. Le informazioni relative al cliente che ha espresso interesse nel ricevere il prodotto vengono condivise con l'utente. Il lead si trova al vertice del grafico a imbuto relativo all'acquisizione dei clienti.

    ![Dynamics 365, Contatta](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Un cliente seleziona **Scarica adesso** (oppure seleziona **Crea** nel [portale di Azure](https://portal.azure.com/)) per ricevere l'offerta. Si tratta di un lead *attivo*. Le informazioni relative al cliente che ha iniziato a distribuire il prodotto vengono condivise con l'utente.

    ![SQL Pulsante Scarica adesso](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Pulsante Crea di Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Un cliente seleziona **test drive** o **versione di valutazione gratuita** per provare l'offerta. I test drive o le versioni di valutazione gratuita sono opportunità accelerate per condividere immediatamente la propria attività con potenziali clienti senza limiti di ingresso.

    ![Pulsante Test drive di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Pulsante Versione di valutazione gratuita di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connettersi al sistema CRM

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informazioni sui dati dei lead

Ogni lead ricevuto durante il processo di acquisizione del cliente presenta dati in campi specifici. Il primo campo da cercare è il campo `LeadSource`, che segue il formato: **Origine-Azione** | **Offerta**.

**Origini**: Il valore di questo campo viene popolato in base al marketplace che ha generato il lead. I valori possibili sono `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Azioni**: Il valore di questo campo viene popolato in base all'azione svolta dal cliente nel marketplace che ha generato il lead.

I valori possibili sono:

- **"INS"** : è l'acronimo per *installazione*. Questa azione si trova in Azure Marketplace o AppSource quando un cliente acquisisce il prodotto.
- **"PLT"** : significa *versione di valutazione condotta dal partner*. Questa azione si trova in AppSource quando un cliente seleziona l'opzione **Contatta**.
- **"DNC"** : sta per *non contattare*. L'azione si trova in AppSource quando un partner che è stato elencato in modo incrociato nella pagina app riceve una richiesta per essere contattato. Viene condiviso l'annuncio che il cliente è stato elencato nell'app, ma non deve essere contattato.
- **"Crea"** : questa azione si trova solo nel portale di Azure e si genera quando un cliente acquista l'offerta dal proprio account.
- **"StartTestDrive"** : questa azione riguarda solo l'opzione **Test drive** e si genera quando un cliente avvia il test drive.

**Offerte**: nel marketplace commerciale potrebbero esistere più offerte. Il valore di questo campo viene popolato in base all'offerta che ha generato il lead. L'ID editore e l'ID offerta vengono entrambi inviati in questo campo e sono valori specificati al momento della pubblicazione dell'offerta nel marketplace.

Negli esempi seguenti vengono illustrati i valori nel formato previsto `publisherid.offerid`: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informazioni sul cliente

Le informazioni sul cliente vengono inviate tramite più campi. L'esempio seguente mostra le informazioni sul cliente contenute in un lead.

- FirstName: John
- LastName: Smith
- E-mail: jsmith\@microsoft.com
- Telefono: 1234567890
- Paese: Stati Uniti
- Società: Microsoft
- Titolo: CTO

>[!NOTE]
>Non tutti i dati dell'esempio precedente sono sempre disponibili per ogni lead. Poiché si otterranno lead dai vari passaggi, come specificato nella sezione "Generare lead clienti", il modo migliore per gestire i lead è deduplicare i record e personalizzare i follow-up. In questo modo, ogni cliente riceve un messaggio appropriato e si crea una relazione univoca.

## <a name="best-practices-for-lead-management"></a>Procedure consigliate per la gestione dei lead

Di seguito sono riportati alcuni suggerimenti per guidare i lead nel ciclo di vendita:

- **Processo**: definire un processo di vendita chiaro, con attività cardine, analisi e una chiara proprietà del team.
- **Qualificazione**: definire i prerequisiti, che indicano se un lead era completo. Assicurarsi che i rappresentanti di vendita o marketing qualifichino i lead con attenzione prima di prenderli in considerazione per il processo di vendita completo.
- **Completamento**: non dimenticare di seguirli entro 24 ore. Si otterrà il lead nel CRM preferito immediatamente dopo la distribuzione di un test drive da parte del cliente; inviare un messaggio di posta elettronica all'interno di mentre sono ancora a caldo. Richiede la pianificazione di una telefonata per comprendere meglio se il prodotto è una soluzione ottimale per il problema. Si prevede che la transazione tipica richieda numerose chiamate di completamento.
- **Seguire**: consolidare i lead in modo da raggiungere un margine di profitto superiore. Archiviarli senza bombardarli. Si consiglia di inviare messaggi di posta elettronica almeno alcune volte prima di chiuderli; non abbandonarsi dopo il primo tentativo. Tenere presente che questi clienti hanno partecipato direttamente al prodotto e hanno dedicato tempo a una versione di valutazione gratuita; sono ottime prospettive.

Dopo che è stata eseguita la configurazione tecnica, incorporare i lead nella strategia di vendita e marketing e nei processi operativi correnti. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dati sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Sono graditi [feedback](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti che consentano al team di vendita di sviluppare i lead del marketplace commerciale in modo ottimale.

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti e risoluzione dei problemi di gestione dei lead](../lead-management-faq.md)