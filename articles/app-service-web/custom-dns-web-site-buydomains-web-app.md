---
title: Come acquistare un nome di dominio personalizzato nelle app Web di Servizio app di Azure
description: Informazioni su come acquistare un nome di dominio personalizzato con un'app Web in Servizio app di Azure.
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 47a7bea963e5ed79f8d08e9b20bdde8003a2ce4f
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Quando si crea un'app Web, Azure la assegna a un sottodominio di azurewebsites.net. Ad esempio, se l'app Web è denominata **contoso**, l'URL corrisponderà a **contoso.azurewebsites.net**. Azure assegna anche un indirizzo IP virtuale.

Per un'app Web di produzione, è probabile che l'amministratore desideri che gli utenti vedano un nome di dominio personalizzato. Questo articolo spiega come acquistare e configurare un dominio personalizzato con le [app Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). 

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="overview"></a>Panoramica
Se l'app Web non ha un nome di dominio, è possibile acquistarne facilmente uno sul [portale di Azure](https://portal.azure.com/). Durante il processo di acquisto è possibile scegliere che i record DNS WWW e di radice di dominio vengano associati automaticamente all'app Web. È possibile anche gestire il dominio direttamente dal portale di Azure.

Seguire questa procedura per acquistare i nomi di dominio e assegnarli all'app Web.

1. Accedere al [portale di Azure](https://portal.azure.com/) nel browser.
2. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati**
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)
3. Nella pagina **Domini personalizzati** fare clic su **Acquista domini**.
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)
4. Nella casella di testo della pagina **Acquista domini** immettere il nome del dominio che si vuole acquistare e premere `Enter`. I domini disponibili suggeriti vengono visualizzati sotto la casella di testo. Selezionare il dominio che si vuole acquistare. È possibile scegliere di acquistare più domini in una sola volta. 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)
5. Scegliere **Informazioni di contatto** e compilare il modulo di informazioni di contatto del dominio.
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)
   
   > [!IMPORTANT]
   > Compilare tutti i campi obbligatori con la massima correttezza possibile, in particolare l'indirizzo di posta elettronica. Se si acquista il dominio senza protezione della privacy, è possibile che venga visualizzata una richiesta di verifica dell'indirizzo di posta elettronica prima che il dominio diventi attivo. In alcuni casi, eventuali dati non corretti relativi alle informazioni di contatto determinano un errore nell'acquisto dei domini. 
   > 
   > 
6. A questo punto è possibile scegliere di
   
    a) Rinnovare automaticamente il dominio ogni anno
   
    b) Acconsentire esplicitamente alla protezione della privacy, funzionalità inclusa GRATUITAMENTE nel prezzo di acquisto (tranne per domini di primo livello il cui registro non supporta la privacy, ad esempio .co.in, .co.uk e così via)  
   
    c) Assegnare nomi host predefiniti per domini WWW e radice all'app Web corrente 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
   
   > [!NOTE]
   > L'opzione C configura le associazioni DNS e le associazioni del nome host automaticamente.  In questo modo, è possibile accedere all'app Web usando il dominio personalizzato non appena l'acquisto è completato (solo in alcuni casi si verificano ritardi di propagazione DNS). Se l'app Web è protetta da Gestione traffico di Azure, non sarà disponibile un'opzione per assegnare il dominio radice, in quanto i record A non funzionano con Gestione traffico. È sempre possibile assegnare i domini o i sottodomini acquistati tramite un'app Web a un'altra app Web e viceversa. Per altre informazioni, vedere il passaggio 8. 
   > 
   > 
7. Fare clic su **Seleziona** nella pagina **Acquista domini**. Le informazioni di acquisto potranno essere visualizzate nella pagina **Conferma acquisto**. Se si accettano le condizioni legali e si fa clic su **Acquista**, l'ordine viene inviato ed è possibile monitorare il processo di acquisto in **Notifica**. L'acquisto del dominio può richiedere alcuni minuti. 
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)
8. Se un dominio è stato ordinato correttamente, è possibile gestirlo e assegnarlo all'app Web. Fare clic sui tre puntini (**...**) a destra del dominio. Questa operazione consente di **annullare l'acquisto** o **gestire il dominio**. Fare clic su **Gestisci dominio** per poter associare il **sottodominio** all'app Web nella pagina **Gestisci dominio**. Per associare un **sottodominio** a un'altra app Web, eseguire questo passaggio dal contesto dell'app Web corrispondente. Mentre si esegue questa operazione è possibile assegnare il dominio all'endpoint di Gestione traffico (se l'app Web è protetta da Gestione traffico) selezionando il nome di Gestione traffico dal menu a discesa. In questo modo, il dominio o sottodominio viene automaticamente assegnato a tutte le app Web protette dall'endpoint di Gestione traffico. 
   
    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)
   
   > [!NOTE]
   > Per ottenere il rimborso completo, è possibile annullare l'acquisto entro cinque giorni, al termine dei quali non sarà più possibile annullare acquisto. In alternativa, è possibile eliminare il dominio. Eliminando un dominio lo si rilascia dalla sottoscrizione personale senza essere rimborsati e il dominio torna disponibile per l'acquisto. 
   > 
   > 

Al termine della configurazione, il nome di dominio personalizzato viene elencato nella sezione **Associazioni nome host** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

## <a name="what-happens-to-the-custom-domain-you-bought"></a>Cosa accade al dominio personalizzato acquistato
Il dominio personalizzato acquistato nella pagina **Domini personalizzati ed SSL** è associato alla sottoscrizione di Azure. Come risorsa di Azure, questo dominio personalizzato è separato e indipendente dall'app del servizio app per cui è stato inizialmente acquistato il dominio. Ciò significa che:

* All'interno del portale di Azure è possibile usare il dominio personalizzato per più app del servizio app, non soltanto per quella per cui è stato acquistato il dominio personalizzato. 
* È possibile gestire tutti i domini personalizzati acquistati nella sottoscrizione di Azure usando la pagina **Domini personalizzati ed SSL** di *qualsiasi* app del servizio app presente in tale sottoscrizione.
* È possibile assegnare qualsiasi applicazione del servizio app dalla stessa sottoscrizione di Azure a un sottodominio all'interno del dominio personalizzato.
* Se si decide di eliminare un'app del servizio app, è possibile scegliere di non eliminare il dominio personalizzato ad essa associato. In questo modo, è possibile continuare a usare tale dominio per altre app.

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>Non è possibile visualizzare il dominio personalizzato acquistato
Se il dominio personalizzato è stato acquistato nella pagina **Domini personalizzati ed SSL** ma non viene visualizzato in **Domini gestiti**, verificare quanto segue:

* È possibile che la creazione del dominio personalizzato non sia stata completata. Selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
* È possibile che per qualche motivo la creazione del dominio personalizzato non sia riuscita. Selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
* È possibile che la creazione del dominio personalizzato sia stata completata ma che la pagina non sia ancora stata aggiornata. Provare a riaprire la pagina **Domini personalizzati ed SSL**.
* È possibile che a un certo punto il dominio personalizzato sia stato eliminato. Verificare i log di controllo facendo clic su **Impostazioni** > **Log di controllo** nella pagina principale dell'app. 
* La pagina **Domini personalizzati ed SSL** che si sta esaminando potrebbe appartenere a un'app creata in un'altra sottoscrizione di Azure. Passare a un'altra app in una sottoscrizione differente ed esaminare la pagina **Domini personalizzati ed SSL** relativa all'app.  
    All'interno del portale non è possibile visualizzare o gestire domini personalizzati creati in una sottoscrizione di Azure diversa da quella dell'app. Se si fa clic su **Gestione avanzata** nella pagina **Gestisci dominio** del dominio, tuttavia, si viene reindirizzati al sito Web del provider di dominio, dove è possibile [configurare manualmente il dominio personalizzato come qualsiasi dominio personalizzato esterno](app-service-web-tutorial-custom-domain.md) per app create in una sottoscrizione diversa di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come acquistare e configurare un certificato SSL per il nuovo dominio personalizzato.

> [!div class="nextstepaction"]
> [Acquistare e configurare un certificato SSL per il servizio app di Azure](web-sites-purchase-ssl-web-site.md)

