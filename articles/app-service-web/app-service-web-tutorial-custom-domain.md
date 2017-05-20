---
title: "Esecuzione del mapping di un nome DNS personalizzato esistente con un’app Web di Azure | Microsoft Docs"
description: Informazioni su come aggiungere un nome di dominio DNS personalizzato esistente, ad esempio il dominio personale, nell&quot;app Web, nel back-end dell&quot;app per dispositivi mobili o nell&quot;app per le API del Servizio app di Azure.
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
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Esecuzione del mapping di un nome DNS personalizzato esistente con un app Web di Azure

Questa esercitazione illustra come eseguire il mapping di un nome DNS personalizzato esistente con un’[app Web di Azure](app-service-web-overview.md). 

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il mapping di un sottodominio (ad esempio, `www.contoso.com`) usando un record CNAME
> * Eseguire il mapping di un dominio radice (ad esempio, `contoso.com`) usando un record A
> * Eseguire il mapping di un dominio con caratteri jolly (ad esempio, `*.contoso.com`) usando un record CNAME
> * Automatizzare il mapping dei domini con script

È possibile utilizzare un **record CNAME** o un **record A** per eseguire il mapping di un nome DNS personalizzato con un servizio app.

> [!NOTE]
> È consigliabile usare un record CNAME per tutti i nomi DNS personalizzati tranne il dominio radice (ad esempio, `contoso.com`). 
> 
> 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario accedere al registro DNS per il provider di dominio (ad esempio, GoDaddy) e alle autorizzazioni per modificare la configurazione per il dominio. 

Ad esempio, per aggiungere le voci DNS per `contoso.com` e `www.contoso.com`, è necessario avere l’accesso per configurare le impostazioni DNS per il dominio radice `contoso.com`. 

> [!NOTE]
> Qualora non si disponga di un nome di dominio esistente, prendere in considerazione la seguente [Esercitazione sul dominio del Servizio app](custom-dns-web-site-buydomains-web-app.md) per acquistare un dominio tramite il portale di Azure. 
>
>

## <a name="prepare-your-app"></a>Preparare l'app
Per eseguire il mapping di un nome DNS personalizzato, il [piano di Servizio App](https://azure.microsoft.com/pricing/details/app-service/) deve essere un livello a pagamento (**Condiviso**, **Base**, **Standard** o **Premium**). In questo passaggio, assicurarsi che l'app del Servizio app si trovi nel piano tariffario supportato.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il Portale di Azure. A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com) con un account di Azure.

### <a name="navigate-to-your-app"></a>Accedere all'app
Nel menu a sinistra fare clic su **Servizi app**, quindi sul nome dell'app.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

Viene visualizzato il pannello di gestione dell'app del servizio app (_pannello_: pagina del portale che si apre orizzontalmente).  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

Nel riquadro di spostamento a sinistra del pannello dell'app, scorrere fino alla sezione **Impostazioni** e selezionare **Scala verticalmente (piano di servizio app)**.

![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Verificare che l'app non sia inclusa nel livello **Gratuito**. Il livello corrente dell'app è evidenziato da una casella blu scuro. 

![Controllo del piano tariffario](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Il DNS personalizzato non è supportato nel livello **Gratuito**. Se è necessario passare a un livello superiore, consultare la sezione successiva. In caso contrario, chiudere il pannello **Scegliere un piano tariffario** e passare direttamente alle sezioni [Esecuzione del mapping di un record CNAME](#cname) o [Esecuzione del mapping di record A](#a).

### <a name="scale-up-your-app-service-plan"></a>Passare a un piano di servizio app superiore

Selezionare uno dei livelli non gratuiti (**Condiviso**, **Base**, **Standard** o **Premium**). 

Fare clic su **Seleziona**.

![Controllo del piano tariffario](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando viene visualizzata la notifica di seguito, l'operazione di passaggio al piano superiore è stata completata.

![Conferma operazione di scalabilità](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Esecuzione del mapping di un record CNAME

Nell'esempio dell'esercitazione, si desidera aggiungere un record CNAME al sottodominio `www` (`www.contoso.com`). 

### <a name="access-dns-records-with-domain-provider"></a>Accedere ai record DNS con il provider di dominio

Accedere prima di tutto al sito Web del provider di dominio.

Individuare la pagina relativa alla gestione dei record DNS. Ogni provider di dominio ha la propria interfaccia per i record DNS, quindi è necessario vedere la documentazione del provider. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. 

Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**. Cercare quindi un collegamento che consente di gestire i record DNS. È possibile che il collegamento abbia un nome simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

La schermata seguente è un esempio di pagina di record DNS:

![Pagina record DNS di esempio](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Nello screenshot di esempio fare clic su **Aggiungi** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Anche in questo caso, vedere la documentazione del provider.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche**. 
>
>

### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Aggiungere un record CNAME per eseguire il mapping di un sottodominio con il nome host predefinito dell'app (`<app_name>.azurewebsites.net`).

Per l’esempio di dominio `www.contoso.com`, il record CNAME deve far puntare il nome `www` a `<app_name>.azurewebsites.net`.

La pagina dei record DNS è simile alla seguente:

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Abilitazione mapping dei record CNAME nell’app

È ora possibile aggiungere i nomi DNS configurati all'app.

Nel riquadro di navigazione sinistro del pannello dell'app, fare clic su **Domini personalizzati**. 

![Menu del dominio personalizzato](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Nel pannello **Domini personalizzati** dell'app è necessario aggiungere il nome DNS personalizzato completo (`www.contoso.com`) all'elenco.

Fare clic sull'icona **+** accanto ad **Aggiungi il nome host**.

![Aggiunta del nome host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digitare il nome di dominio completo per cui è stato configurato in precedenza il record CNAME, ad esempio `www.contoso.com`, quindi fare clic su **Convalida**.

In caso contrario, il pulsante **Aggiungere nome host** viene attivato. 

Assicurarsi che **Tipo di record del nome host** sia impostato su **Record CNAME (esempio.com)**.

Fare clic su **Aggiungi il nome host** per aggiungere il nome DNS all'app.

![Aggiunta del nome DNS all'app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

![Record CNAME aggiunto](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

![Errore di verifica](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Esecuzione del mapping di un record A

Nell'esempio dell'esercitazione, si desidera aggiungere un record A al dominio radice, `contoso.com`. 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>Copia dell’indirizzo IP dell’app

Per eseguire il mapping di un record A, è necessario l'indirizzo IP esterno dell'applicazione. Si trova nel pannello **Domini personalizzati**.

Nel riquadro di navigazione sinistro del pannello dell'app, fare clic su **Domini personalizzati**. 

![Menu del dominio personalizzato](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Nella pagina **Domini personalizzati**, copiare l'indirizzo IP dell'applicazione.

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>Accedere ai record DNS con il provider di dominio

Accedere prima di tutto al sito Web del provider di dominio.

Individuare la pagina relativa alla gestione dei record DNS. Ogni provider di dominio ha la propria interfaccia per i record DNS, quindi è necessario vedere la documentazione del provider. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. 

Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**. Cercare quindi un collegamento che consente di gestire i record DNS. È possibile che il collegamento abbia un nome simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

La schermata seguente è un esempio di pagina di record DNS:

![Pagina record DNS di esempio](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Nello screenshot di esempio fare clic su **Aggiungi** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Anche in questo caso, vedere la documentazione del provider.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche**. 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>Creazione di un record A

Per eseguire il mapping di un record A all'app, il servizio app richiede effettivamente **due** record DNS:

- Un record **A** per eseguire il mapping dell'indirizzo IP dell'app.
- Un record **TXT** per eseguire il mapping del nome host predefinito dell'app `<app_name>.azurewebsites.net`. Questo record consente al servizio app di verificare la proprietà del dominio personalizzato a cui si desidera eseguire il mapping.

Per l’esempio di dominio `www.contoso.com`, creare i record A e TXT in base alla tabella seguente (`@` rappresenta in genere il dominio radice). 

| Tipo di record | Host | Valore |
| - | - | - |
| Una  | `@` | Indirizzo IP ricavato da [Copia dell’indirizzo IP dell’app](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

La pagina dei record DNS è simile alla seguente:

![Pagina dei record DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

### <a name="enable-the-a-record-mapping-in-your-app"></a>Abilitazione mapping dei record A nell’app

È ora possibile aggiungere i nomi DNS configurati all'app.

Nella pagina **Domini personalizzati** dell'app nel portale di Azure è necessario aggiungere il nome DNS personalizzato completo (`contoso.com`) all'elenco.

Fare clic sull'icona **+** accanto ad **Aggiungi il nome host**.

![Aggiunta del nome host](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Digitare il nome di dominio completo per cui è stato configurato in precedenza il record A, ad esempio `contoso.com`, quindi fare clic su **Convalida**.

In caso contrario, il pulsante **Aggiungere nome host** viene attivato. 

Assicurarsi che **Tipo di record del nome host** sia impostato su **Record A (esempio.com)**.

Fare clic su **Aggiungi il nome host** per aggiungere il nome DNS all'app.

![Aggiunta del nome DNS all'app](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

![Record A aggiunto](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

![Errore di verifica](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Esecuzione del mapping di un dominio con caratteri jolly

È inoltre possibile eseguire il mapping di un [DNS con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (ad esempio, `*.contoso.com`) con l'applicazione del Servizio app. 

La seguente procedura dimostra come eseguire il mapping di un dominio con caratteri jolly `*.contoso.com` tramite un record CNAME. 

### <a name="access-dns-records-with-domain-provider"></a>Accedere ai record DNS con il provider di dominio

Accedere prima di tutto al sito Web del provider di dominio.

Individuare la pagina relativa alla gestione dei record DNS. Ogni provider di dominio ha la propria interfaccia per i record DNS, quindi è necessario vedere la documentazione del provider. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. 

Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**. Cercare quindi un collegamento che consente di gestire i record DNS. È possibile che il collegamento abbia un nome simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

La schermata seguente è un esempio di pagina di record DNS:

![Pagina record DNS di esempio](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Nello screenshot di esempio fare clic su **Aggiungi** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Anche in questo caso, vedere la documentazione del provider.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche**. 
>
>

### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Aggiungere un record CNAME per eseguire il mapping di un nome con caratteri jolly con il nome host predefinito dell'app (`<app_name>.azurewebsites.net`).

Per l’esempio di dominio `*.contoso.com`, il record CNAME deve far puntare il nome `*` a `<app_name>.azurewebsites.net`.

La pagina dei record DNS è simile alla seguente:

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Abilitazione mapping dei record CNAME nell’app

Ora è possibile aggiungere qualsiasi sottodominio che corrisponde al nome con caratteri jolly.

Per l’esempio con carattere jolly `*.contoso.com`, ora è possibile aggiungere `sub1.contoso.com` e `sub2.contoso.com`. 

Nel riquadro di navigazione sinistro del pannello dell'app, fare clic su **Domini personalizzati**. 

![Menu del dominio personalizzato](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Fare clic sull'icona **+** accanto ad **Aggiungi il nome host**.

![Aggiunta del nome host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digitare il nome di dominio completo per un sottodominio corrispondente al dominio con caratteri jolly (ad esempio, `sub1.contoso.com`), quindi fare clic su **Convalida**.

In caso contrario, il pulsante **Aggiungere nome host** viene attivato. 

Assicurarsi che **Tipo di record del nome host** sia impostato su **Record CNAME (esempio.com)**.

Fare clic su **Aggiungi il nome host** per aggiungere il nome DNS all'app.

![Aggiunta del nome DNS all'app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

È possibile fare di nuovo clic sull’icona **+** per aggiungere un altro nome host corrispondente al dominio con caratteri jolly.

Ad esempio, aggiungere `sub2.contoso.com` utilizzando la stessa procedura illustrata precedentemente.

![Record CNAME aggiunto](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Prova nel browser

Nel browser, selezionare il/i nome/i DNS configurato/i in precedenza (`contoso.com` e `www.contoso.com`).

![Passare all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la gestione dei domini personalizzati con gli script, usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app. 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

Per ulteriori informazioni, vedere [Esecuzione del mapping di un dominio personalizzato con un’app Web](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Per ulteriori informazioni, vedere [Assegnazione di un dominio personalizzato a un’app Web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Eseguire il mapping di un sottodominio usando un record CNAME
> * Eseguire il mapping di un dominio radice usando un record A
> * Eseguire il mapping di un dominio con caratteri jolly usando un record CNAME
> * Automatizzare il mapping dei domini con script

Passare all'esercitazione successiva per apprendere come associare un certificato SSL personalizzato.

> [!div class="nextstepaction"]
> [Associare un certificato SSL personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-ssl.md)

