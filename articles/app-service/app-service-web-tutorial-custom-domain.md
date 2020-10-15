---
title: 'Esercitazione: Eseguire il mapping di un nome DNS personalizzato esistente'
description: Informazioni su come aggiungere un nome di dominio DNS personalizzato esistente, ad esempio il dominio personale, in un'app Web, nel back-end dell'app per dispositivi mobili o nell'app per le API del servizio app di Azure.
keywords: servizio app, servizio app di Azure, mapping di dominio, nome di dominio, dominio esistente, nome host
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 6a74f105525ec8ce28559b47ed4fc9624f518a06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488338"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Esercitazione: Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come eseguire il mapping di un nome DNS (Domain Name System) personalizzato esistente al servizio app.

![Screenshot che mostra come spostarsi nel portale di Azure per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Eseguire il mapping di un sottodominio (ad esempio, `www.contoso.com`) usando un record CNAME.
> * Eseguire il mapping di un dominio radice (ad esempio, `contoso.com`) usando un record A.
> * Eseguire il mapping di un dominio con caratteri jolly (ad esempio, `*.contoso.com`) usando un record CNAME.
> * Reindirizzare l'URL predefinito a una directory personalizzata.
> * Automatizzare il mapping dei domini con script.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Creare un'app del servizio app](./index.yml) oppure usare un'app creata per un'altra esercitazione.
* Acquistare un nome di dominio e assicurarsi di avere accesso al registro DNS per il provider del dominio, ad esempio GoDaddy.

  Ad esempio, per aggiungere le voci DNS per `contoso.com` e `www.contoso.com` è necessario essere autorizzati a configurare le impostazioni DNS per il dominio radice `contoso.com`.

  > [!NOTE]
  > Se non è disponibile un nome di dominio esistente, può essere utile [acquistare un dominio tramite il portale di Azure](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Preparare l'app

Per eseguire il mapping di un nome DNS personalizzato a un'app Web, il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) dell'app Web sia un livello a pagamento (Condiviso, Basic, Standard, Premium o A consumo per Funzioni di Azure). In questo passaggio, verificare che l''app del servizio app si trovi nel piano tariffario supportato.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selezionare l'app nel portale di Azure

1. Cercare e selezionare **Servizi app**.

   ![Screenshot che mostra la selezione di Servizi app.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Nella pagina **Servizi app** selezionare il nome dell'app di Azure.

   ![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Viene visualizzata la pagina di gestione dell'app del servizio app.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

1. Nel riquadro sinistro della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **Aumenta prestazioni (piano di servizio app)** .

   ![Screenshot che mostra il menu Aumenta prestazioni (piano di servizio app).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app non sia inclusa nel livello **F1**. Il DNS personalizzato non è supportato nel livello **F1**.

   ![Screenshot che mostra i piani tariffari consigliati.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se il piano di servizio app non è incluso nel livello **F1**, chiudere la pagina **Aumenta** e passare a [Esecuzione del mapping di un record CNAME](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

1. Selezionare uno dei livelli non gratuiti (**D1**, **B1**, **B2**, **B3** o uno qualsiasi dei livelli della categoria **Produzione**). Per altre opzioni, selezionare **Visualizza opzioni aggiuntive**.

1. Selezionare **Applica**.

   ![Screenshot che mostra la verifica dei piani tariffari.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

   ![Screenshot che mostra la conferma dell'operazione di aumento delle prestazioni.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Ottenere un ID di verifica dominio

Per aggiungere un dominio personalizzato all'app, è necessario verificare la proprietà del dominio aggiungendo un ID verifica come record TXT al provider di dominio. Nel riquadro sinistro della pagina dell'app selezionare **Domini personalizzati**. Nella pagina **Domini personalizzati** copiare l'ID nella casella **ID di verifica dominio personalizzato** per il passaggio successivo.

![Screenshot che mostra l'ID nella casella ID di verifica dominio personalizzato.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> L'aggiunta dell'ID di verifica al dominio personalizzato può impedire voci DNS inesatte e consente di evitare l'acquisizione della proprietà dei sottodomini. Per altre informazioni su questa minaccia comune con gravità elevata, vedere [Acquisizione della proprietà dei sottodomini](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Eseguire il mapping del dominio

È possibile utilizzare un record CNAME o un record A per eseguire il mapping di un nome DNS personalizzato al servizio app. Seguire la procedura corrispondente:

- [Esecuzione del mapping di un record CNAME](#map-a-cname-record)
- [Esecuzione del mapping di un record A](#map-an-a-record)
- [Esecuzione del mapping di un dominio con caratteri jolly usando un record CNAME](#map-a-wildcard-domain)

> [!NOTE]
> Usare i record CNAME per tutti i nomi DNS personalizzati tranne i domini radice (ad esempio, `contoso.com`). Per i domini radice, usare i record A.

### <a name="map-a-cname-record"></a>Esecuzione del mapping di un record CNAME

Nell'esempio dell'esercitazione si aggiunge un record CNAME per il sottodominio `www`, ad esempio `www.contoso.com`.

Se è presente un sottodominio diverso da `www`, sostituire `www` con il sottodominio (ad esempio, con `sub` se il dominio personalizzato è `sub.constoso.com`).

#### <a name="access-dns-records-with-a-domain-provider"></a>Accedere ai record DNS con un provider di dominio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Eseguire il mapping di un sottodominio al nome di dominio predefinito dell'app (`<app-name>.azurewebsites.net`, dove `<app-name>` è il nome dell'app). Per creare un mapping CNAME per il sottodominio `www`, creare due record:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Il mapping del dominio stesso. |
| TXT | `asuid.www` | [ID verifica ottenuto in precedenza](#get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid.<subdomain>` per verificare la proprietà del dominio personalizzato. |

Dopo aver aggiunto i record CNAME e TXT, la pagina dei record DNS è simile all'esempio seguente:

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Abilitare il mapping dei record CNAME in Azure

1. Nel riquadro sinistro della pagina dell'app nel portale di Azure selezionare **Domini personalizzati**.

    ![Screenshot che mostra il menu Domini personalizzati.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Nella pagina **Domini personalizzati** dell'app aggiungere il nome DNS personalizzato completo (`www.contoso.com`) all'elenco.

1. Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'elemento per l'aggiunta del nome host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digitare il nome di dominio completo per il quale è stato aggiunto un record CNAME, ad esempio `www.contoso.com`.

1. Selezionare **Convalida**. Viene visualizzata la pagina **Aggiungi dominio personalizzato**.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **CNAME (www\.example.com o qualsiasi sottodominio**). Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra il pulsante Aggiungi dominio personalizzato.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    ![Screenshot che mostra l'aggiunta del record CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md).

    Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

    ![Screenshot che mostra un errore di verifica.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Esecuzione del mapping di un record A

Nell'esempio dell'esercitazione si aggiunge un record A per il dominio radice, ad esempio `contoso.com`.

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Copiare l'indirizzo IP dell'app

Per eseguire il mapping di un record A, è necessario l'indirizzo IP esterno dell'app. È possibile trovare questo indirizzo IP nella pagina **Domini personalizzati** dell'app nel portale di Azure.

1. Nel riquadro sinistro della pagina dell'app nel portale di Azure selezionare **Domini personalizzati**.

   ![Screenshot che mostra il menu Domini personalizzati.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Nella pagina **Domini personalizzati** copiare l'indirizzo IP dell'app.

   ![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Accedere ai record DNS con un provider di dominio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Creazione di un record A

Per eseguire il mapping di un record A a un'app, in genere al dominio radice, creare due record:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| Una | `@` | Indirizzo IP ricavato da [Copiare l'indirizzo IP dell'app](#info) | Il mapping di dominio stesso (`@` rappresenta in genere il dominio radice). |
| TXT | `asuid` | [ID verifica ottenuto in precedenza](#get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid.<subdomain>` per verificare la proprietà del dominio personalizzato. Per il dominio radice usare `asuid`. |

> [!NOTE]
> Per aggiungere un sottodominio (come `www.contoso.com`) usando un record A al posto di un [record CNAME](#map-a-cname-record) consigliato, il record A e il record TXT devono corrispondere invece alla tabella seguente:
>
> | Tipo di record | Host | valore |
> | - | - | - |
> | Una | `www` | Indirizzo IP ricavato da [Copiare l'indirizzo IP dell'app](#info) |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

Dopo aver aggiunto i record, la pagina dei record DNS è simile all'esempio seguente:

![Screenshot che mostra una pagina di record DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Abilitare il mapping del record A nell'app

Nella pagina **Domini personalizzati** dell'app nel portale di Azure aggiungere all'elenco il nome DNS personalizzato completo, ad esempio `contoso.com`.

1. Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digitare il nome di dominio completo per il quale è stato configurato il record A, ad esempio `contoso.com`. 

1. Selezionare **Convalida**. Viene visualizzata la pagina **Aggiungi dominio personalizzato**.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **Record A (esempio.com).** Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome DNS all'app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    ![Screenshot che mostra l'aggiunta di un record A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md).
    
    Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.
    
    ![Screenshot che mostra un errore di verifica.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Esecuzione del mapping di un dominio con caratteri jolly

Nell'esempio dell'esercitazione si esegue il mapping di un [nome DNS con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record), ad esempio `*.contoso.com`, all'app del servizio app aggiungendo un record CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Accedere ai record DNS con un provider di dominio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Eseguire il mapping di un nome con caratteri jolly `*` al nome di dominio predefinito dell'app (`<app-name>.azurewebsites.net`, dove `<app-name>` è il nome dell'app). Per eseguire il mapping del nome con caratteri jolly, creare due record:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Il mapping del dominio stesso. |
| TXT | `asuid` | [ID verifica ottenuto in precedenza](#get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid` per verificare la proprietà del dominio personalizzato. |

Per l'esempio di dominio `*.contoso.com`, il record CNAME eseguirà il mapping del nome `*` a `<app-name>.azurewebsites.net`.

Dopo aver aggiunto il record CNAME, la pagina dei record DNS è simile all'esempio seguente:

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Abilitare il mapping dei record CNAME nell'app

È ora possibile aggiungere qualsiasi sottodominio che corrisponde al nome con caratteri jolly nell'app, ad esempio `sub1.contoso.com`, `sub2.contoso.com` e `*.contoso.com` corrispondono tutti a `*.contoso.com`.

1. Nel riquadro sinistro della pagina dell'app nel portale di Azure selezionare **Domini personalizzati**.

    ![Screenshot che mostra il menu Domini personalizzati.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digitare un nome di dominio completo corrispondente al dominio con caratteri jolly, ad esempio `sub1.contoso.com`, quindi selezionare **Convalida**.

    Il pulsante **Aggiungi dominio personalizzato** viene attivato.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **CNAME (www\.example.com o qualsiasi sottodominio**). Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome DNS all'app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

1. Fare di nuovo clic sull'icona **+** per aggiungere un altro dominio personalizzato corrispondente al dominio con caratteri jolly. Ad esempio, aggiungere `sub2.contoso.com`.

    ![Screenshot che mostra l'aggiunta di un record CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Eseguire test in un browser

Passare ai nomi DNS configurati in precedenza, ad esempio `contoso.com`, `www.contoso.com`, `sub1.contoso.com` e `sub2.contoso.com`.

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Risolvere l'errore 404 "Non trovato"

Se si riceve un errore HTTP 404 (Non trovato) quando si seleziona l'URL del dominio personalizzato, verificare che il dominio venga risolto nell'indirizzo IP dell'app usando <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Se non viene risolto, il problema può dipendere da uno dei motivi seguenti:

- Nel dominio personalizzato configurato manca un record A o un record CNAME.
- Il browser client ha memorizzato nella cache l'indirizzo IP precedente del dominio. Cancellare la cache e testare nuovamente la risoluzione del DNS. In un computer Windows cancellare la cache con `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Eseguire la migrazione di un dominio attivo

Per eseguire la migrazione di un sito live e del relativo nome di dominio DNS al Servizio app senza tempi di inattività, vedere [Eseguire la migrazione di un nome DNS attivo al Servizio app di Azure](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Reindirizzare a una directory personalizzata

Per impostazione predefinita, il servizio app indirizza le richieste Web alla directory radice del codice dell'app. Alcuni framework Web, però, non vengono avviati nella directory radice. Ad esempio, [Laravel](https://laravel.com/) viene avviato nella sottodirectory `public`. Per continuare con l'esempio relativo al DNS `contoso.com`, un'app di questo tipo è accessibile all'indirizzo `http://contoso.com/public`, ma si vuole indirizzare l'URL `http://contoso.com` alla directory `public`. Questa operazione non interessa la risoluzione del DNS, ma implica la personalizzazione della directory virtuale.

Per personalizzare una directory virtuale, selezionare **Impostazioni applicazione** nel riquadro sinistro della pagina dell'app Web.

Nella parte inferiore della pagina la directory virtuale radice `/` punta per impostazione predefinita a `site\wwwroot`, che è la directory radice del codice dell'app. Modificare il percorso in modo che la directory punti, ad esempio, a `site\wwwroot\public` e salvare le modifiche.

![Screenshot che mostra la personalizzazione di una directory virtuale.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Al termine dell'operazione, l'app dovrebbe restituire la pagina corretta nel percorso radice, ad esempio `http://contoso.com`.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la gestione dei domini personalizzati con gli script, usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Per altre informazioni, vedere [Eseguire il mapping di un dominio personalizzato a un'app Web](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Per ulteriori informazioni, vedere [Assegnazione di un dominio personalizzato a un’app Web](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Eseguire il mapping di un sottodominio usando un record CNAME.
> * Eseguire il mapping di un dominio radice usando un record A.
> * Eseguire il mapping di un dominio con caratteri jolly usando un record CNAME.
> * Reindirizzare l'URL predefinito a una directory personalizzata.
> * Automatizzare il mapping dei domini con script.

Continuare con l'esercitazione successiva per informazioni su come associare un certificato TLS/SSL personalizzato a un'app Web.

> [!div class="nextstepaction"]
> [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md)
