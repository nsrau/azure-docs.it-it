---
title: Acquistare un nome di dominio personalizzato per app Web Azure
description: Informazioni su come acquistare un nome di dominio personalizzato con un'app Web in Servizio app di Azure.
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 44d350d2d098be14ad254066a8528fe117200ec4
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Acquistare un nome di dominio personalizzato per app Web Azure

I domini del servizio app (anteprima) sono i domini di primo livello che sono gestiti direttamente in Azure. Semplificano le operazioni di gestione dei domini personalizzati per le [app Web di Azure](app-service-web-overview.md). In questa esercitazione viene illustrato come acquistare un dominio del servizio app e come assegnare i nomi DNS alle app Web di Azure.

Questo articolo è per il Servizio app di Azure (app Web, app per le API, app per dispositivi mobili, app per la logica). Per una macchina virtuale di Azure o per Archiviazione di Azure, vedere [Assign App Service domain to Azure VM or Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/) (Assegnare il dominio del servizio app alla macchina virtuale di Azure o ad Archiviazione di Azure). Per i servizi cloud, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Creare un'app del servizio app](/azure/app-service/) oppure usare un'app creata per un'altra esercitazione.

## <a name="prepare-the-app"></a>Preparare l'app

Per usare domini personalizzati nelle app Web di Azure, è necessario che il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) dell'app Web sia un livello a pagamento (**Condiviso**, **Basic**, **Standard** o **Premium**). In questo passaggio, verificare che l'app Web sia supportata dal piano tariffario adeguato.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Passare all'app nel portale di Azure

Dal menu a sinistra selezionare **Servizi app** e quindi selezionare il nome dell'app.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

Viene visualizzata la pagina di gestione dell'app del servizio app.  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

Nel riquadro di spostamento a sinistra della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **Scala verticalmente (piano di servizio app)**.

![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app non sia inclusa nel livello **Gratuito**. Il DNS personalizzato non è supportato nel livello **Gratuito**. 

![Controllare il piano tariffario](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se il piano di servizio app non è **Gratuito**, chiudere la pagina **Scegliere il piano tariffario** e passare a [Acquista il dominio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

Selezionare uno dei livelli non gratuiti (**Condiviso**, **Base**, **Standard** o **Premium**). 

Fare clic su **Seleziona**.

![Controllare il piano tariffario](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

![Conferma operazione di scalabilità](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Acquistare il dominio

### <a name="sign-in-to-azure"></a>Accedere ad Azure
Aprire il [portale di Azure](https://portal.azure.com/) e accedere con l'account Azure.

### <a name="launch-buy-domains"></a>Avvio di Acquistare un dominio
Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Nella pagina **Domini personalizzati** fare clic su **Acquista domini**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

### <a name="configure-the-domain-purchase"></a>Configurare l’acquisto del dominio

Nella pagina **Dominio del servizio app**, digitare il nome di dominio che si desidera acquistare nel riquadro **Ricerca dominio** , quindi digitare `Enter`. I domini disponibili suggeriti vengono visualizzati sotto la casella di testo. Selezionare uno o più domini che si desidera acquistare. 
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

Scegliere **Informazioni di contatto** e compilare il modulo di informazioni di contatto del dominio. Al termine, fare clic su **OK** per tornare alla pagina del dominio di servizio app.
   
È molto importante compilare tutti i campi obbligatori con la massima correttezza possibile. I dati non corretti relativi alle informazioni di contatto causano errori nell'acquisto dei domini. 

Selezionare quindi le opzioni desiderate per il dominio. Vedere la tabella seguente per alcune spiegazioni:

| Impostazione | Valore consigliato | Descrizione |
|-|-|-|
|Rinnovo automatico | **Abilitazione** | Rinnova il dominio del servizio App automaticamente ogni anno. Al momento del rinnovo, sulla carta di credito viene addebitato lo stesso prezzo di acquisto. |
|Protezione della privacy | Abilita | Optare per "Protezione della privacy", che è incluso nel prezzo di acquisto _gratuitamente_ (ad eccezione dei domini di primo livello il cui registro non supporta la protezione della privacy, ad esempio _.co.in_, _.co.uk_ e così via). |
| Assegnare i nomi host predefiniti | **www** e **@** | Selezionare le associazioni del nome host desiderate, se necessario. Una volta completata l'operazione di acquisto del dominio, l'app Web è accessibile tramite i nomi host selezionati. Se l'app Web è nascosta da [Gestione traffico di Microsoft Azure](https://azure.microsoft.com/services/traffic-manager/), non viene visualizzata l'opzione per assegnare il dominio radice (@), perché Gestione traffico non supporta record A. Al termine dell'acquisto di un dominio, è possibile apportare modifiche alle assegnazioni di nome host. |

### <a name="accept-terms-and-purchase"></a>Accettare i termini di acquisto

Fare clic su **Termini legali** per leggere i termini e le tariffe, quindi fare clic su **Acquista**.

> [!NOTE]
> I domini del servizio app usano il DNS di Azure per ospitare i domini. Oltre alla tariffa di registrazione del dominio, si applicano le spese di utilizzo per il DNS di Azure. Per altre informazioni, vedere la pagina relativa ai [prezzi del DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

Nella pagina **Dominio di servizio app** fare clic su **OK**. Mentre l'operazione è in corso, vedrai le notifiche seguenti:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Verifica dei nomi host

Se sono stati assegnati nomi host predefiniti all'app Web, è anche possibile visualizzare una notifica di esito positivo per ogni nome host selezionato. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

I nomi host selezionati vengono visualizzati nella pagina **Domini personalizzati**, nella sezione **Nomi host**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Per verificare i nomi host, navigare nei nomi host elencati dal browser. Nell'esempio nella schermata precedente, navigare in _kontoso.net_ e _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Assegnare i nomi host per app Web

Se si sceglie di non assegnare uno o più nomi host predefiniti per l'app Web durante il processo di acquisto oppure se è necessario assegnare un nome host non elencato, è possibile assegnare un nome host in qualsiasi momento.

È inoltre possibile assegnare i nomi host nel dominio del servizio app a qualsiasi altra app Web. I passaggi variano a seconda se il dominio del servizio app e l'app Web appartengono alla stessa sottoscrizione.

- Sottoscrizione diversa: eseguire il mapping personalizzato dei record DNS dal dominio del servizio app all'app Web analogamente a un dominio acquistato esternamente. Per informazioni sull'aggiunta di nomi DNS personalizzati a un dominio del servizio app, vedere [Gestione dei record DNS personalizzati](#custom). Per eseguire il mapping di un dominio esterno acquistato per un'app Web, vedere [Eseguire il mapping di un nome DNS personalizzato esistente per le app Web di Azure](app-service-web-tutorial-custom-domain.md). 
- In caso di medesima sottoscrizione, eseguire la procedura seguente.

### <a name="launch-add-hostname"></a>Avviare Aggiungi nome host
Nella pagina **servizi App**, selezionare il nome dell'app Web a cui si desidera assegnare i nomi host, selezionare **Impostazioni**, quindi selezionare **Domini personalizzati**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Verificare che il dominio acquistato sia presente nella sezione **domini del servizio app**, ma non selezionarlo. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Tutti i domini del servizio app con la stessa sottoscrizione vengono visualizzati nella pagina dell'app Web **Domini personalizzati**. Se il dominio è nella sottoscrizione dell'app Web, ma non è possibile visualizzarlo nella pagina dell'app Web **Domini personalizzati**, provare a riaprire la pagina **Domini personalizzati** pagina o aggiornare la pagina Web. Inoltre, selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
>
>

Selezionare **Aggiungi il nome host**.

### <a name="configure-hostname"></a>Configurare il nome host
Nella finestra di dialogo **Aggiungere nome host**, digitare il nome di dominio completo del dominio del servizio app o di qualsiasi sottodominio. ad esempio:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Al termine, selezionare **Convalida**. Il tipo di record del nome host viene selezionato automaticamente.

Selezionare **Aggiungi il nome host**.

Una volta completata l'operazione, viene visualizzata una notifica di esito positivo per il nome di host assegnato.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Chiudere Aggiungi il nome host
Nella pagina **Aggiungi il nome host**, assegnare qualsiasi nome host all'app Web, in base alle proprie esigenze. Al termine, chiudere la pagina **Aggiungi il nome host**.

Viene visualizzato il nome host appena assegnato nella pagina dell’app **Domini personalizzati**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Verifica dei nomi host

Navigare tra i nomi host elencati nel browser. Nell'esempio della schermata precedente, navigare in _abc.kontoso.net_.

<a name="custom" />

## <a name="manage-custom-dns-records"></a>Gestire i record DNS personalizzati

In Azure, i record DNS per un dominio del servizio app vengono gestiti tramite [DNS di Azure](https://azure.microsoft.com/services/dns/). È possibile aggiungere, rimuovere e aggiornare i record DNS, esattamente come per un dominio acquistato esternamente.

### <a name="open-app-service-domain"></a>Aprire Dominio del servizio app

Nel portale di Azure, nel menu a sinistra, selezionare **Più servizi** > **Domini del servizio app**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selezionare il dominio da gestire. 

### <a name="access-dns-zone"></a>Accesso alla Zona DNS

Nel menu a sinistra del dominio, selezionare **Zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Questa azione permette di aprire la pagina [Zona DNS](../dns/dns-zones-records.md) del dominio del servizio App nel DNS di Azure. Per informazioni su come modificare i record DNS, vedere [Come gestire le zone DNS nel portale di Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Annullare l'acquisto (elimina dominio)

Dopo aver acquistato il dominio del servizio app, si hanno a disposizione cinque giorni per annullare l'acquisto e ricevere un rimborso completo. Dopo cinque giorni, è possibile eliminare il dominio del servizio app, ma non è possibile ricevere un rimborso.

### <a name="open-app-service-domain"></a>Aprire Dominio del servizio app

Nel portale di Azure, nel menu a sinistra, selezionare **Più servizi** > **Domini del servizio app**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selezionare il dominio che si desidera annullare o eliminare. 

### <a name="delete-hostname-bindings"></a>Eliminare associazioni nome host

Nel menu a sinistra del dominio, selezionare **Associazioni nome host**. Le associazioni nome host da tutti i servizi Azure sono elencate di seguito.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Non è possibile eliminare il dominio del servizio app finché non vengono eliminate tutte le associazioni nome host.

Eliminare le associazioni nome host selezionando **...** > **Elimina**. Dopo l'eliminazione di tutte le associazioni, selezionare **Salva**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Annullare o eliminare

Nel menu a sinistra del dominio, selezionare **Panoramica**. 

Se non è trascorso il periodo di cancellazione del dominio acquistato, selezionare **Annulla acquisto**. In caso contrario, viene visualizzato il pulsante **Elimina**. Per eliminare il dominio senza rimborso, selezionare **Elimina**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Selezionare **OK** per confermare l'operazione. Se non si desidera continuare, fare clic su qualsiasi punto all'esterno della finestra di dialogo di conferma.

Al termine dell’operazione, il dominio viene rilasciato dalla sottoscrizione e può essere riacquistato dagli altri utenti. 

