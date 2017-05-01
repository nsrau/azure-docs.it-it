---
title: Eseguire il mapping di un nome DNS personalizzato per un&quot;app Web di Azure | Microsoft Docs
description: Informazioni su come aggiungere un nome di dominio DNS personalizzato, ad esempio il dominio personale, nell&quot;app Web, nel back-end dell&quot;app per dispositivi mobili, nell&quot;app per le API del Servizio app di Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>Eseguire il mapping di un nome DNS personalizzato per un'app Web di Azure

Questa esercitazione illustra come eseguire il mapping di un nome di DNS personalizzato, ad esempio dominio personale, nell'app Web, nel back-end dell'app per dispositivi mobili o nell'app per le API nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md). 

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Questa esercitazione segue lo scenario di esempio di mapping di due nomi DNS per un'app nel servizio app:

- `contoso.com`: un dominio radice. Si userà un [record A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) per eseguirne il mapping in Azure. 
- `www.contoso.com`: un sottodominio di `contoso.com`. È possibile usare un [record A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) o [record CNAME](https://en.wikipedia.org/wiki/CNAME_record).

L'esercitazione mostra anche come eseguire il mapping di un [DNS con carattere jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record) per il servizio app (ad esempio `*.contoso.com`).

## <a name="before-you-begin"></a>Prima di iniziare

Poiché questa esercitazione illustra come eseguire il mapping di un nome DNS per il Servizio app di Azure, è necessario avere accesso amministrativo alla pagina di configurazione DNS per il provider del rispettivo dominio (ad esempio GoDaddy). Per aggiungere, ad esempio, un mapping per `contoso.com` e `www.contoso.com`, è necessario poter configurare le voci DNS per `contoso.com`.

Se non è ancora stato registrato un dominio con un provider di dominio, è sufficiente [acquistare un dominio direttamente da Azure ed eseguirne il mapping nell'app](custom-dns-web-site-buydomains-web-app.md) in qualsiasi momento.

## <a name="step-1---prepare-your-app"></a>Passaggio 1: preparare l'app
Per eseguire il mapping di un nome DNS personalizzato, il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) deve avere il livello **Condiviso** o superiore. In questo passaggio assicurarsi che l'app di Azure si trovi nel piano tariffario supportato.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Aprire il Portale di Azure. 

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com) con un account di Azure.

### <a name="navigate-to-your-app"></a>Accedere all'app
Nel menu a sinistra fare clic su **Servizio app** e quindi sul nome dell'app di Azure.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app.  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario
Nella pagina **Panoramica**, che viene visualizzata per impostazione predefinita, assicurarsi che l'app abbia il livello **Gratuito**.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Il DNS personalizzato non è supportato nel livello **Gratuito**. Se è necessario passare a un livello superiore, consultare la sezione successiva. In caso contrario, andare direttamente al [passaggio 2]().

### <a name="scale-up-your-app-service-plan"></a>Passare a un piano di servizio app superiore

Per passare a un piano superiore, fare clic su **Passa a un piani superiore (piano di servizio app)** nel riquadro a sinistra.

Selezionare il livello a cui si desidera passare. Ad esempio, selezionare **Condiviso**. Quando si è pronti, fare clic su **Seleziona**.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando viene visualizzata la notifica di seguito, l'operazione di passaggio al piano superiore è stata completata.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>Passaggio 2: ottenere informazioni sul mapping

In questo passaggio si ottengono le informazioni necessarie per creare i record DNS in un secondo momento. 

### <a name="open-the-custom-domain-ui"></a>Aprire l'interfaccia utente del dominio personalizzato

Nel pannello dell'app, fare clic su **Domini personalizzati** nel menu. 

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>Copiare le informazioni di mapping

Nella pagina **Domini personalizzati**, copiare l'**indirizzo IP** dell'app e il nome host predefinito in **Nomi host assegnati al sito**.

È necessario l'indirizzo IP in un secondo momento per il mapping del record A , mentre il nome host predefinito sarà necessario per il mapping del record CNAME.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>Passaggio 3: aggiungere i record DNS 

In questo passaggio, è possibile aggiungere i record DNS necessari per eseguire il mapping DNS personalizzato per l'app. Eseguire questo passaggio con il proprio provider di dominio.

### <a name="access-dns-records-with-domain-provider"></a>Accedere ai record DNS con il provider di dominio

Accedere prima di tutto al sito Web del provider di dominio.

Individuare quindi la pagina relativa alla gestione dei record DNS. Ogni provider di dominio ha la propria interfaccia per i record DNS, quindi è necessario vedere la documentazione del provider. In generale è necessario individuare i collegamenti o le aree del sito denominate **Nome dominio**, **DNS** o **Gestione server nomi**. Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**. 

Cercare quindi un collegamento che consente di gestire i record DNS. È possibile che il collegamento abbia un nome simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

Lo screenshot seguente è un esempio di pagina di record DNS:

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Nello screenshot di esempio fare clic su **Aggiungi** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Anche in questo caso, vedere la documentazione del provider.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche** in seguito alla loro creazione. 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>Creare un record A

Nell'esempio dell'esercitazione, si desidera aggiungere un record A al dominio radice, `contoso.com`. 

Per eseguire il mapping di un record A all'app, il servizio app richiede effettivamente _due_ record DNS:

- Un record **A** per eseguire il mapping dell'indirizzo IP dell'app.
- Un record **TXT** per eseguire il mapping del nome host predefinito dell'app. Questo record consente al servizio app di verificare la proprietà del dominio personalizzato a cui si desidera eseguire il mapping.

Nella tabella seguente viene illustrato come configurare il mapping di un record A per i tipi di dominio supportati (`@` rappresenta in genere il dominio radice). 

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo di dominio</th>
    <th>Tipo di record</th>
    <th>Host</th>
    <th>Valore</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">Dominio radice<br>(ad esempio <code>contoso.com</code>)</td>
    <td>Una </td>
    <td>@</td>
    <td>Indirizzo IP del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td>Nome host predefinito del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Sottodominio<br>(ad esempio <code>www.contoso.com</code>)</td>
    <td>Una </td>
    <td>www</td>
    <td>Indirizzo IP del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td>Nome host predefinito del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Dominio con caratteri jolly<br>(ad esempio <code>*.contoso.com</code>)</td>
    <td>Una </td>
    <td>\*</td>
    <td>Indirizzo IP del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td>Nome host predefinito del <a href="#info">passaggio 2</a></td>
  </tr>
</table>

Per il dominio di esempio `contoso.com`, la pagina dei record DNS è simile al seguente screenshot:

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>Creare un record CNAME
Nell'esempio dell'esercitazione, si desidera aggiungere un record CNAME al dominio radice, `contoso.com`. 

A differenza del [mapping di un record A](#a), il mapping di un record CNAME per l'app non richiede alcun record aggiuntivo.

> [!IMPORTANT]
> È consigliabile usare un mapping CNAME. Se si elimina e si ricrea l'app oppure se da un piano tariffario di livello host si torna al piano **Condiviso** , l'indirizzo IP virtuale dell'app potrebbe cambiare. Il mapping del record CNAME è valido per la modifica, mentre il mapping del record A è potenzialmente non valido da un nuovo indirizzo IP. 
>
> Tuttavia _non_ creare un record CNAME per il dominio radice, ovvero il "record radice". Per altre informazioni, vedere [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)(Perché non è possibile usare un record CNAME nel dominio radice).
> Per eseguire il mapping di un dominio radice all'app Azure, usare un [record A](#a) .
> 
> 

Nella tabella seguente viene illustrato come configurare il mapping di un record CNAME per i tipi di dominio supportati.

Configurare il record CNAME come indicato di seguito.

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo di dominio</th>
    <th>Host CNAME</th>
    <th>Valore CNAME</th>
  </tr>
  <tr>
    <td>Sottodominio<br>(ad esempio <code>www.contoso.com</code>)</td>
    <td>www</td>
    <td>Nome host predefinito del <a href="#info">passaggio 2</a></td>
  </tr>
  <tr>
    <td>Dominio con caratteri jolly<br>(ad esempio <code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td>Nome host predefinito del <a href="#info">passaggio 2</a></td>
  </tr>
</table>

Per il dominio di esempio `www.contoso.com`, la pagina dei record DNS è simile al seguente screenshot:

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>Passaggio 4: abilitare il DNS personalizzato nell'app

È ora possibile aggiungere i nomi DNS configurati (ad esempio `contoso.com` e `www.contoso.com`) all'app.

Nella pagina **Domini personalizzati** del portale di Azure (vedere il [passaggio 1](#info)) è necessario aggiungere all'elenco il nome di dominio completo (FQDN) del dominio personalizzato.

### <a name="add-the-a-hostname-to-your-app"></a>Aggiungere il nome di un host A all'app

Fare clic sull'icona **+** accanto ad **Aggiungi il nome host**.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Digitare il nome di dominio completo per cui è stato configurato in precedenza il record A, ad esempio `contoso.com`, quindi fare clic su **Convalida**.

Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/verification-error.png)

In caso contrario, il pulsante **Aggiungere nome host** viene attivato. 

Assicurarsi che **Tipo di record del nome host** sia impostato su **Record A (esempio.com)**.

Fare clic su **Aggiungi il nome host** per aggiungere il nome DNS all'app.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>Aggiungere il nome di un host CNAME all'app

Fare clic sull'icona **+** accanto ad **Aggiungi il nome host**.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digitare il nome di dominio completo per cui è stato configurato in precedenza il record CNAME, ad esempio `www.contoso.com`, quindi fare clic su **Convalida**.

Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

In caso contrario, il pulsante **Aggiungere nome host** viene attivato. 

Assicurarsi che **Tipo di record del nome host** sia impostato su **Record A (esempio.com)**.

Fare clic su **Aggiungi il nome host** per aggiungere il nome DNS all'app.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>Passaggio 5: test nel browser

Nel browser, selezionare il/i nome/i DNS configurato/i in precedenza (`contoso.com` e `www.contoso.com`).

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>Altre risorse

[Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md)

