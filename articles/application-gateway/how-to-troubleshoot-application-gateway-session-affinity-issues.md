---
title: Risolvere i problemi di affinità di sessione
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come risolvere i problemi di affinità di sessione in applicazione Azure gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047976"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Risolvere i problemi di affinità di sessione del gateway applicazione Azure

Informazioni su come diagnosticare e risolvere i problemi di affinità di sessione con applicazione Azure gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overview

L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso server. Usando cookie gestiti dal gateway, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso server per l'elaborazione. Questo è importante nei casi in cui lo stato della sessione viene salvato in locale sul server per una sessione utente.

## <a name="possible-problem-causes"></a>Possibili cause del problema

Il problema relativo alla gestione dell'affinità di sessione basata su cookie potrebbe verificarsi a causa dei motivi principali seguenti:

- Impostazione "affinità basata su cookie" non abilitata
- L'applicazione non è in grado di gestire l'affinità basata su cookie
- L'applicazione usa l'affinità basata su cookie, ma le richieste continuano a rimbalzare tra i server back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verificare che l'impostazione "affinità basata su cookie" sia abilitata

Talvolta è possibile che si verifichino problemi di affinità di sessione quando si dimentica di abilitare l'impostazione "affinità basata su cookie". Per determinare se è stata abilitata l'impostazione "affinità basata su cookie" nella scheda Impostazioni HTTP del portale di Azure, seguire le istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nel riquadro di **spostamento a sinistra** fare clic su **tutte le risorse**. Fare clic sul nome del gateway applicazione nel pannello tutte le risorse. Se nella sottoscrizione selezionata sono già presenti diverse risorse, è possibile immettere il nome del gateway applicazione nel **filtro per nome...** per accedere con facilità al gateway applicazione.

3. Selezionare la scheda **impostazioni http** in **Impostazioni**.

   ![risoluzione dei problemi-Session-Affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Fare clic su **appGatewayBackendHttpSettings** sul lato destro per verificare se è stata selezionata l'opzione **abilitato** per affinità basata su cookie.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



È inoltre possibile verificare che il valore di "**CookieBasedAffinity**" sia impostato su *abilitato*in "**backendHttpSettingsCollection**" utilizzando uno dei metodi seguenti:

- Eseguire [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) in PowerShell
- Esaminare il file JSON usando il modello di Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>L'applicazione non è in grado di gestire l'affinità basata su cookie

#### <a name="cause"></a>Causa

Il gateway applicazione può eseguire solo l'affinità basata sulla sessione usando un cookie.

#### <a name="workaround"></a>Soluzione alternativa

Se l'applicazione non è in grado di gestire l'affinità basata su cookie, è necessario usare un servizio di bilanciamento del carico interno o esterno di Azure o un'altra soluzione di terze parti.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>L'applicazione usa l'affinità basata su cookie, ma le richieste continuano a rimbalzare tra i server back-end

#### <a name="symptom"></a>Sintomo

È stata abilitata l'impostazione di affinità basata su cookie, quando si accede al gateway applicazione usando un URL con nome breve in Internet Explorer, ad esempio: [http://website](http://website/) , la richiesta sta ancora rimbalzando tra i server back-end.

Per identificare questo problema, seguire le istruzioni:

1. Eseguire una traccia del debugger Web sul "client", che si connette all'applicazione dietro il gateway applicazione (in questo esempio si usa Fiddler).
    **Suggerimento** Se non si sa come usare Fiddler, selezionare l'opzione "**desidero raccogliere il traffico di rete e analizzarlo usando il debugger Web**" nella parte inferiore.

2. Controllare e analizzare i log di sessione per determinare se i cookie forniti dal client dispongono dei dettagli ARRAffinity. Se non si trovano i dettagli di ARRAffinity, ad esempio "**ARRAffinity =** *ARRAffinityValue*" all'interno del set di cookie, significa che il client non sta rispondendo con il cookie ARRA, fornito dal gateway applicazione.
    Ad esempio:

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

L'applicazione continua a provare a impostare il cookie per ogni richiesta fino a quando non riceve la risposta.

#### <a name="cause"></a>Causa

Questo problema si verifica perché Internet Explorer e altri browser non possono archiviare o utilizzare il cookie con un URL con nome breve.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema è necessario accedere al gateway applicazione usando un nome di dominio completo. Usare, ad esempio, [http://website.com](https://website.com/) o [http://appgw.website.com](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Log aggiuntivi per la risoluzione dei problemi

È possibile raccogliere log aggiuntivi e analizzarli per risolvere i problemi correlati all'affinità di sessione basata su cookie

### <a name="analyze-application-gateway-logs"></a>Analizzare i log del gateway applicazione

Per raccogliere i log del gateway applicazione, seguire le istruzioni:

Abilitare la registrazione tramite il portale di Azure

1. Nella [portale di Azure](https://portal.azure.com/)individuare la risorsa e quindi fare clic su **log di diagnostica**.

   Per il gateway applicazione sono disponibili tre log: log di accesso, log delle prestazioni, log del firewall

2. Per iniziare a raccogliere i dati, fare clic **su attiva diagnostica**.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Il pannello **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Per impostare l'area di lavoro, fare clic su **Configura** in **log Analytics** . Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![risoluzione dei problemi-Session-Affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confermare le impostazioni e quindi fare clic su **Salva**.

   ![risoluzione dei problemi-Session-Affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visualizzare e analizzare i log di accesso del gateway applicazione

1. Nella finestra di portale di Azure sotto la visualizzazione delle risorse del gateway applicazione selezionare **log di diagnostica** nella sezione **monitoraggio** .

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Sul lato destro selezionare "**ApplicationGatewayAccessLog**" nell'elenco a discesa in **categorie di log.**  

   ![risoluzione dei problemi-Session-Affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Nell'elenco log di accesso del gateway applicazione fare clic sul log che si vuole analizzare ed esportare, quindi esportare il file JSON.

4. Convertire il file JSON esportato nel passaggio 3 in un file CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati.

5. Controllare i dati seguenti:

- **CLIENTIP**: si tratta dell'indirizzo IP client del client che si connette.
- **ClientPort** : porta di origine dal client di connessione per la richiesta.
- **RequestQuery** : indica il server di destinazione in cui viene ricevuta la richiesta.
- **Server-routing**: istanza del pool back-end che la richiesta riceve.
- **X-AzureApplicationGateway-LOG-ID**: ID correlazione usato per la richiesta. Può essere usato per risolvere i problemi di traffico nei server back-end. Ad esempio: X-AzureApplicationGateway-CACHE-HIT = 0 & SERVER-ROUTINGd = 10.0.2.4.

  - **SERVER-STATUS**: codice di risposta HTTP ricevuto dal gateway applicazione dal back-end.

  ![risoluzione dei problemi-Session-Affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vengono visualizzati due elementi provenienti dalla stessa porta di ClientIP e client e vengono inviati allo stesso server back-end, significa che il gateway applicazione è stato configurato correttamente.

Se vengono visualizzati due elementi provenienti dalla stessa porta di ClientIP e client e vengono inviati ai server back-end diversi, ovvero la richiesta sta rimbalzando tra i server back-end, selezionare "l'**applicazione usa l'affinità basata su cookie, ma le richieste ancora che passano tra i server back-end**" nella parte inferiore per risolvere il problema.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Usare il debugger Web per acquisire e analizzare i traffici HTTP o HTTPS

Gli strumenti di debug Web come Fiddler consentono di eseguire il debug delle applicazioni Web acquisendo il traffico di rete tra Internet e i computer di test. Questi strumenti consentono di esaminare i dati in entrata e in uscita quando il browser li riceve/li invia. Fiddler, in questo esempio, ha l'opzione di riproduzione HTTP che può aiutare a risolvere i problemi sul lato client con le applicazioni Web, in particolare per il tipo di autenticazione problema.

Usare il debugger Web di propria scelta. In questo esempio verrà usato Fiddler per acquisire e analizzare il traffico http o HTTPS, seguire le istruzioni:

1. Scaricare lo strumento Fiddler all'<https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Scegliere Fiddler4 se nel computer di acquisizione è installato .NET 4. In caso contrario, scegliere fiddler2.

2. Fare clic con il pulsante destro del mouse sul file eseguibile di installazione ed eseguire come amministratore per installare.

    ![risoluzione dei problemi-Session-Affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando si apre Fiddler, dovrebbe iniziare automaticamente l'acquisizione del traffico (si noti l'acquisizione nell'angolo inferiore sinistro). Premere F12 per avviare o arrestare l'acquisizione del traffico.

    ![risoluzione dei problemi-Session-Affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. È molto probabile che si sia interessati al traffico HTTPS decrittografato ed è possibile abilitare la decrittografia HTTPS selezionando **strumenti** > **Opzioni Fiddler**e selezionando la casella " **decrittografare il traffico HTTPS**".

    ![risoluzione dei problemi-Session-Affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. È possibile rimuovere le precedenti sessioni non correlate prima di riprodurre il problema facendo clic su **X** (icona) > **Rimuovi tutto** come segue screenshot: 

    ![risoluzione dei problemi-Session-Affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Dopo aver riprodotto il problema, salvare il file per verificarlo selezionando **file** > **Salva** > **tutte le sessioni..** . 

    ![risoluzione dei problemi-Session-Affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Controllare e analizzare i log di sessione per determinare il problema.

    Ad esempio:

- **Esempio A:** Si trova un log di sessione che la richiesta viene inviata dal client e passa all'indirizzo IP pubblico del gateway applicazione e si fa clic su questo log per visualizzare i dettagli.  Sul lato destro, i dati nella casella inferiore corrispondono a quelli restituiti dal gateway applicazione al client. Selezionare la scheda "RAW" e determinare se il client riceve un "**set-cookie: ARRAffinity =** *ARRAffinityValue*". Se non è presente alcun cookie, l'affinità di sessione non è impostata o il gateway applicazione non applica al client il cookie.

   > [!NOTE]
   > Questo valore ARRAffinity è l'ID cookie, che il gateway applicazione imposta per il client da inviare a un server back-end specifico.

   ![risoluzione dei problemi-Session-Affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Esempio B:** Il log della sessione successivo seguito da quello precedente è il client che risponde al gateway applicazione, che ha impostato ARRAAFFINITY. Se il cookie-ID ARRAffinity corrisponde, il pacchetto deve essere inviato allo stesso server back-end usato in precedenza. Controllare le varie righe successive della comunicazione http per verificare se il cookie ARRAffinity del client sta cambiando.

   ![risoluzione dei problemi-Session-Affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Per la stessa sessione di comunicazione, il cookie non deve essere modificato. Controllare la casella superiore sul lato destro, selezionare la scheda "cookie" per verificare se il client sta usando il cookie e inviarlo di nuovo al gateway applicazione. In caso contrario, il browser client non mantiene e usa il cookie per le conversazioni. In alcuni casi, il client potrebbe trovarsi.

 

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
