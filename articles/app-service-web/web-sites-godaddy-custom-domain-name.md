---
title: Configurazione di un nome di dominio personalizzato nel servizio app di Azure (GoDaddy)
description: Informazioni su come usare un nome di dominio di GoDaddy con le app Web di Azure
services: app-service
documentationcenter: ''
author: erikre
manager: wpickett
editor: jimbe

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin

---
# <a name="configure-a-custom-domain-name-in-azure-app-service-(purchased-directly-from-godaddy)"></a>Configurare un nome di dominio personalizzato nel servizio app di Azure (acquistato direttamente da GoDaddy)
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Se si è acquistato un dominio tramite App Web del servizio app di Azure, fare riferimento all'ultimo passaggio di [Acquistare un dominio per app Web](custom-dns-web-site-buydomains-web-app.md).

Questo articolo contiene istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato direttamente da [GoDaddy](https://godaddy.com) con [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714).

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Informazioni sui record DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Aggiunta di un record DNS per il dominio personalizzato
Per associare il dominio personalizzato a un'app Web nel servizio app, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1. Accedere al proprio account presso GoDaddy.com e selezionare **Mio account**, quindi **Gestisci i miei domini**. Infine, selezionare il menu a discesa relativo al nome di dominio da usare con l'app Web di Azure e selezionare **Gestisci DNS**.
   
    ![pagina del domino personalizzato per GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)
2. Nella pagina **Dettagli dominio** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio.
   
    ![Scheda DNS Zone File](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)
   
    Selezionare **Aggiungi record** a un record esistente.
   
    Per **modificare** un record esistente, selezionare l'icona della penna e del foglio accanto al record.
   
   > [!NOTE]
   > Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor), ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera utilizzare è già presente, modificare il record esistente anziché crearne uno nuovo.
   > 
   > 
3. Per aggiungere un record, è necessario innanzitutto selezionare un tipo di record.
   
    ![select record type](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)
   
    Quindi, è necessario specificare l'**Host** (il dominio o il sottodominio personalizzato) e l'opzione **Points to**.
   
    ![add zone record](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)
   
   * Quando si aggiunge un **record A (host)**, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **Points to* (Punta a)* sull'indirizzo IP dell'app Web di Azure.
   * Quando si aggiunge un **record CNAME (alias)**, è necessario impostare il campo **Host** sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Points to** (Punta a) sul nome di dominio **.azurewebsites.net** dell'app Web di Azure. Ad esempio, **contoso.azurewebsites.net**.
4. Fare clic su **Aggiungi utente**.
5. Selezionare **TXT** come tipo di record, quindi specificare un valore **Host** di **@** e un valore **Points to** (Punta a) di **&lt;nomeappweb&gt;.azurewebsites.net**.
   
   > [!NOTE]
   > Questo record TXT viene usato da Azure per convalidare la proprietà del dominio descritto dal record A o il primo record TXT. Una volta eseguito il mapping del dominio all'app Web nel portale di Azure, questa voce del record TXT può essere rimossa.
   > 
   > 
6. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Fine** per salvare le modifiche.

<a name="enabledomain"></a>

## <a name="enable-the-domain-name-on-your-web-app"></a>Abilitazione del nome del dominio nell'app Web
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="what's-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--HONumber=Oct16_HO2-->


