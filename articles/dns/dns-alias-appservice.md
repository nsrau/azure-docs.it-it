---
title: Ospitare app Web di Azure con carico bilanciato nel dominio radice
description: Usare un record alias del DNS di Azure per ospitare app Web con carico bilanciato nel dominio radice
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: b08eae072c2fbe420401424baf97a25b4cbbe87b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790743"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Ospitare app Web di Azure con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di qualsiasi elemento eccetto un record A/AAAA nel dominio radice, Un esempio di dominio radice è contoso.com. Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro Gestione traffico. Non è possibile puntare al profilo di Gestione traffico dal record di dominio radice. I proprietari delle applicazioni devono quindi usare una soluzione alternativa. Un reindirizzamento al livello applicazione deve reindirizzare dal dominio radice a un altro dominio. Un esempio è un reindirizzamento da contoso.com a www\.contoso.com. Questo approccio presenta un singolo punto di guasto in termini di funzionalità di reindirizzamento.

Con i record di alias, questo problema non esiste più. I proprietari delle applicazioni possono ora indirizzare i record del dominio radice a un profilo di Gestione traffico con endpoint esterni. I proprietari delle applicazioni possono puntare allo stesso profilo di Gestione traffico che viene usato per qualsiasi altro dominio all'interno della zona DNS.

Ad esempio, contoso.com e www\.contoso.com può puntare allo stesso profilo di Traffic Manager. Ciò avviene fintanto che nel profilo di Gestione traffico sono configurati solo endpoint esterni.

Questo articolo descrive come creare un record alias per il dominio radice e configurare il profilo di Gestione traffico per le app Web.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di un nome di dominio che si possa ospitare in DNS di Azure per il test. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

Il dominio di esempio usato per questa esercitazione è contoso.com, ma in questo caso usare il nome di dominio personale.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse per contenere le risorse utilizzate in questo articolo.

## <a name="create-app-service-plans"></a>Creare piani di servizio app

Creare due piani di servizio app Web nel gruppo di risorse usando la tabella seguente per le informazioni di configurazione. Per altre informazioni sulla creazione di un piano di servizio app, vedere [Gestire un piano di servizio app in Azure](../app-service/app-service-plan-manage.md).


|Name  |Sistema operativo  |Località  |Piano tariffario  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Stati Uniti orientali|Sviluppo/test D1-Shared|
|ASP-02     |Windows|Stati Uniti centrali|Sviluppo/test D1-Shared|

## <a name="create-app-services"></a>Creare Servizi app

Creare due app Web, una in ciascun piano di servizio app.

1. Nell'angolo in alto a sinistra della pagina del portale di Azure fare clic su **Crea una risorsa**.
2. Digitare **App Web** nella barra di ricerca e premere INVIO.
3. Fare clic su **App Web**.
4. Fare clic su **Create**(Crea).
5. Accettare le impostazioni predefinite e usare la tabella seguente per configurare le due app Web:

   |Name<br>(deve essere univoco all'interno di .azurewebsites.net)|Gruppo di risorse |Piano di servizio app/Località
   |---------|---------|---------|
   |App-01|Usa esistente<br>Selezionare un gruppo di risorse|ASP-01(Stati Uniti orientali)|
   |App-02|Usa esistente<br>Selezionare un gruppo di risorse|ASP-02(Stati Uniti centrali)|

### <a name="gather-some-details"></a>Raccogliere alcune informazioni dettagliate

Ora è necessario prendere nota dell'indirizzo IP e del nome host per le app.

1. Aprire il gruppo di risorse e fare clic sulla prima app, in questo esempio **App-01**.
2. Nella colonna sinistra fare clic su **Proprietà**.
3. Prendere nota dell'indirizzo sotto **URL** e del primo indirizzo IP dell'elenco sotto **Indirizzi IP in uscita**. Si useranno queste informazioni in seguito per configurare l'endpoint di Gestione traffico.
4. Ripetere l'operazione per **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un gruppo di Gestione traffico nel gruppo di risorse. Usare le impostazioni predefinite e digitare un nome univoco all'interno dello spazio dei nomi trafficmanager.net.

Per informazioni sulla creazione di un profilo di Traffic Manager, vedere [Guida introduttiva: Creare un profilo di Traffic Manager per un'applicazione web a disponibilità elevata](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Creare endpoint

Ora è possibile creare gli endpoint per le due app Web.

1. Aprire il gruppo di risorse e fare clic sul profilo di Gestione traffico.
2. Nella colonna sinistra fare clic su **Endpoint**.
3. Fare clic su **Aggiungi**.
4. Usare la tabella seguente per configurare gli endpoint:

   |Type  |NOME  |Destinazione  |Località  |Impostazioni intestazione personalizzata|
   |---------|---------|---------|---------|---------|
   |Endpoint esterno     |End-01|Indirizzo IP registrato per App-01|Stati Uniti orientali|host:\<URL registrato per App-01\><br>Esempio: **host:app-01.azurewebsites.net**|
   |Endpoint esterno     |End-02|Indirizzo IP registrato per App-02|Stati Uniti centrali|host:\<URL registrato per App-02\><br>Esempio: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Creare una zona DNS

È possibile usare una zona DNS esistente per il testing o creare una nuova zona. Per creare e delegare una nuova zona DNS in Azure, vedere [esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Aggiungere il set di record di alias

Quando la zona DNS è pronta, è possibile aggiungere un record di alias per il dominio radice.

1. Aprire il gruppo di risorse e fare clic sulla zona DNS.
2. Fare clic su **Set di record**.
3. Aggiungere il set di record usando la tabella seguente:

   |Name  |Type  |Set di record di alias  |Tipo di alias  |Risorsa di Azure|
   |---------|---------|---------|---------|-----|
   |@     |Una|Yes|Risorsa di Azure|Gestione traffico - Profilo personale|

## <a name="add-custom-hostnames"></a>Aggiungere nomi host personalizzati

Aggiungere un nome host personalizzato a entrambe le app Web.

1. Aprire il gruppo di risorse e fare clic sulla prima app Web.
2. Nella colonna sinistra fare clic su **Domini personalizzati**.
3. Fare clic su **Aggiungi il nome host**.
4. Sotto Nome host digitare il nome di dominio. Ad esempio, contoso.com.

   Il dominio deve superare la convalida e mostrare segni di spunta verdi accanto a **Disponibilità nome host** e **Proprietà del dominio**.
5. Fare clic su **Aggiungi il nome host**.
6. Per vedere il nuovo nome host in **Nomi host assegnati al sito**, aggiornare il browser. L'aggiornamento nella pagina non mostra sempre le modifiche apportate immediatamente.
7. Ripetere questa procedura per la seconda app Web.

## <a name="test-your-web-apps"></a>Testare le app Web

A questo punto è possibile eseguire test per verificare che sia possibile raggiungere l'app Web e che l'app sia dotata di bilanciamento del carico.

1. Aprire un Web browser e passare al dominio. Ad esempio, contoso.com. Dovrebbe essere visualizzata la pagina predefinita dell'app Web.
2. Arrestare la prima app Web.
3. Chiudere il Web browser e attendere alcuni minuti.
4. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzata ancora la pagina predefinita dell'app Web.
5. Arrestare la seconda app Web.
6. Chiudere il Web browser e attendere alcuni minuti.
7. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzato l'errore 403 che indica che l'app Web è stata arrestata.
8. Avviare la seconda app Web.
9. Chiudere il Web browser e attendere alcuni minuti.
10. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzata di nuovo la pagina predefinita dell'app Web.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio radice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
