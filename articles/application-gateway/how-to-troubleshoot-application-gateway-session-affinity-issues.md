---
title: Risoluzione dei problemi di affinità sessione Gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come risolvere i problemi di affinità di sessione nel Gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 66f61b5d6fcb86ed93e4dbae802ae7a80613c83d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66397847"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Risolvere i problemi di affinità sessione Gateway applicazione di Azure

Informazioni su come diagnosticare e risolvere i problemi di affinità di sessione con il Gateway applicazione di Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso server. Usando cookie gestiti dal gateway, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso server per l'elaborazione. Questo è importante nei casi in cui lo stato della sessione viene salvato in locale sul server per una sessione utente.

## <a name="possible-problem-causes"></a>Cause di un possibile problema

Il problema nel mantenere l'affinità di sessione basata su cookie può verificarsi a causa dei motivi principali seguenti:

- Impostazione di "affinità basata sui cookie" non è abilitata
- L'applicazione non è possibile gestire l'affinità basata sui cookie
- Applicazione utilizza l'affinità basata sui cookie ma richieste ancora saranno rimbalzati tra i server back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Controllare se è abilitata l'impostazione "affinità basata sui Cookie"

In alcuni casi potrebbero verificarsi i problemi di affinità di sessione quando si dimentica di abilitare l'impostazione "Affinità basata su Cookie". Per determinare se è stata abilitata l'impostazione "Affinità basata su Cookie" nella scheda Impostazioni HTTP nel portale di Azure, seguire le istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nel **riquadro di spostamento sinistro** riquadro, fare clic su **tutte le risorse**. Scegliere il nome del gateway applicazione nel pannello tutte le risorse. Se la sottoscrizione selezionata sono già presenti diverse risorse, è possibile immettere il nome del gateway applicazione nel **Filtra per nome...** per accedere con facilità al gateway applicazione.

3. Selezionare **delle impostazioni HTTP** disponibile nella scheda **impostazioni**.

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Fare clic su **appGatewayBackendHttpSettings** sul lato destro per verificare se è stata selezionata **abilitato** per affinità basata su Cookie.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



È anche possibile controllare il valore della "**CookieBasedAffinity**" è impostata su *Enabled*sotto "**backendHttpSettingsCollection**" usando uno dei metodi seguenti:

- Run [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) in PowerShell
- Esaminare il file JSON usando il modello di Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>L'applicazione non è possibile gestire l'affinità basata sui cookie

#### <a name="cause"></a>Causa

Il gateway applicazione è possibile eseguire solo affinità basata su sessione tramite un cookie.

#### <a name="workaround"></a>Soluzione alternativa

Se l'applicazione non è possibile gestire l'affinità basata su cookie, è necessario usare un servizio di bilanciamento del carico di azure esterno o interno o un'altra soluzione di terze parti.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Applicazione utilizza l'affinità basata sui cookie ma richieste ancora saranno rimbalzati tra i server back-end

#### <a name="symptom"></a>Sintomo

È abilitata l'impostazione di affinità basata sui Cookie, quando si accede al Gateway applicazione usando un URL breve nome in Internet Explorer, ad esempio: [ http://website ](http://website/) , la richiesta è ancora saranno rimbalzati tra i server back-end.

Per identificare questo problema, seguire le istruzioni:

1. Esegue una traccia di debugger web su "Client" che si connette all'applicazione dietro il Gateway(We are using Fiddler in this example) dell'applicazione.
    **Suggerimento** se non si sa come usare il Fiddler, selezionare l'opzione "**si vuole raccogliere il traffico di rete e analizzarli usando il debugger web**" nella parte inferiore.

2. Controllare e analizzare i log di sessione, per determinare se i cookie forniti dal client hanno i dettagli ARRAffinity. Se non si trova i dettagli di ARRAffinity, ad esempio "**ARRAffinity =** *ARRAffinityValue*" all'interno del set di cookie, che significa che il client non sta rispondendo con il cookie ARRA, che avviene tramite il Gateway applicazione.
    Ad esempio:

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

L'applicazione continua a tentare di impostare il cookie a ogni richiesta fino a quando non ottiene risposta.

#### <a name="cause"></a>Causa

Questo problema si verifica perché Internet Explorer e altri browser non vengano archiviati o usare il cookie con un URL di nome breve.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema è necessario accedere al gateway applicazione usando un nome di dominio completo. Ad esempio, usare [ http://website.com ](https://website.com/) oppure [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Log per risolvere i problemi

È possibile raccogliere i log e analizzarli per risolvere l'affinità di sessione basata su cookie correlato problemi

### <a name="analyze-application-gateway-logs"></a>Analizzare i log del Gateway applicazione

Per raccogliere i log del Gateway applicazione, seguire le istruzioni:

Abilitare la registrazione tramite il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/), trovare la risorsa e quindi fare clic su **log di diagnostica**.

   Per il gateway applicazione sono disponibili tre log: Log di accesso, log delle prestazioni, log del Firewall

2. Per iniziare a raccogliere i dati, fare clic su **attivare la diagnostica**.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Il pannello **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Fare clic su **Configure** sotto **Log Analitica** per impostare l'area di lavoro. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confermare le impostazioni e quindi fare clic su **salvare**.

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visualizzare e analizzare i log di accesso del Gateway applicazione

1. Nel portale di Azure in visualizzazione risorse Gateway applicazione, selezionare **log di diagnostica** nel **MONITORING** sezione.

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Sul lato destro, selezionare "**ApplicationGatewayAccessLog**" nell'elenco a discesa in **le categorie di Log.**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Nell'elenco Log di accesso del Gateway applicazione, fare clic sui log da analizzare ed esportare e quindi esportare il file JSON.

4. Convertire il file JSON che è stato esportato nel passaggio 3 in file CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati.

5. Controllare i dati seguenti:

- **ClientIP**– si tratta dell'indirizzo IP client dal client che si connette.
- **ClientPort** -questa è la porta di origine dal client che si connette per la richiesta.
- **RequestQuery** – indica il server di destinazione che viene ricevuta la richiesta.
- **Server-Routed**: Istanza del pool back-end che viene ricevuta la richiesta.
- **X-AzureApplicationGateway-LOG-ID**: ID correlazione usato per la richiesta. Può essere usato per risolvere i problemi di traffico nei server back-end. Ad esempio:  X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: codice di risposta HTTP che il gateway applicazione ha ricevuto dal back-end.

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vengono visualizzati due elementi provengono dalla stessa ClientIP e porta del Client e vengono inviati allo stesso server back-end, che significa che il Gateway applicazione configurato correttamente.

Se vengono visualizzati due elementi provengono dalla stessa ClientIP e porta del Client e vengono inviati ai diversi server back-end, che significa che la richiesta è saranno rimbalzati tra i server back-end, selezionare "**applicazione utilizza l'affinità basata sui cookie ma richieste ancora saranno rimbalzati tra i server back-end**"nella parte inferiore per risolverlo.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Usare il debugger web per acquisire e analizzare il traffico HTTP o HTTPS

Strumenti di debug, ad esempio Fiddler Web, possono agevolare il debug di applicazioni web tramite l'acquisizione del traffico di rete tra i computer Internet e test. Questi strumenti consentono di controllare i dati in ingresso e in uscita perché il browser riceve o invia loro. Fiddler, in questo esempio, ha la possibilità di riproduzione HTTP che può aiutarti a risolvere i problemi lato client con le applicazioni web, in particolare per il tipo di autenticazione del problema.

Usare il debugger web di propria scelta. In questo esempio si userà Fiddler per acquisire e analizzare il traffico http o https, seguire le istruzioni:

1. Scaricare lo strumento Fiddler <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Scegliere Fiddler4 se nel computer di acquisizione è installato .NET 4. In caso contrario, scegliere Fiddler2.

2. Esegui come amministratore per installare e fare clic con il pulsante destro l'eseguibile di programma di installazione.

    ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando si apre Fiddler, dovrebbe essere avviato automaticamente l'acquisizione del traffico (si noti che l'acquisizione nell'angolo inferiore sinistro). Premere F12 per avviare o arrestare l'acquisizione del traffico.

    ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. È probabile che sarà interessato il traffico HTTPS decrittografato ed è possibile abilitare la decrittografia HTTPS selezionando **degli strumenti** > **Fiddler Options**e selezionare la casella " **decrittografare Il traffico HTTPS**".

    ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. È possibile rimuovere le sessioni non correlate precedenti prima di riprodurre il problema facendo **X** (icona) > **Rimuovi tutto** come seguire screenshot: 

    ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Dopo avere riprodotto il problema, salvare il file per la revisione selezionando **File** > **salvare** > **tutte le sessioni...** . 

    ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Controllare e analizzare i log di sessione per determinare il problema.

    Ad esempio:

- **Esempio a:** Presente un log di sessione che la richiesta viene inviata dal client e passa all'indirizzo IP pubblico del Gateway applicazione, fare clic su questo log per visualizzare i dettagli.  A destra, i dati nella parte inferiore della casella sono ciò che il Gateway applicazione restituisce al client. Selezionare la scheda "RAW" e determinare se il client sta ricevendo un "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se non è presente alcun cookie, non è impostata l'affinità di sessione o il Gateway applicazione non viene applicata cookie al client.

   > [!NOTE]
   > Questo valore ARRAffinity è l'id cookie, che consente di impostare il Gateway applicazione per il client da inviare a un determinato server back-end.

   ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Esempio b:** Il log della sessione successivo seguita da uno è il client risponde al Gateway applicazione, che ha impostato il ARRAAFFINITY precedente. Se l'id cookie ARRAffinity corrisponde, il pacchetto deve essere inviato allo stesso server back-end che è stato usato in precedenza. Controllare le successive righe diverse della comunicazione http per vedere se il cookie del client ARRAffinity sta cambiando.

   ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Per la stessa sessione di comunicazione, il cookie deve essere non per modificare. Selezionare la casella nella parte destra superiore, selezionare la scheda "Cookie" per verificare se il client usando i cookie e inviare nuovamente il Gateway applicazione. In caso contrario, il browser client non è mantenere e usando il cookie di conversazioni. In alcuni casi, potrebbe cadere al client.

 

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
