---
title: Risolvere i problemi di affinità delle sessioniTroubleshoot session affinity issues
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come risolvere i problemi di affinità di sessione nel gateway applicazione di AzureThis article provides information on how to troubleshoot session affinity issues in Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047976"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Risolvere i problemi di affinità di sessione di Gateway applicazione di AzureTroubleshoot Azure Application Gateway session affinity issues

Informazioni su come diagnosticare e risolvere i problemi di affinità di sessione con il gateway applicazione di Azure.Learn how to diagnose and resolve session affinity issues with Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso server. Usando cookie gestiti dal gateway, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso server per l'elaborazione. Questo è importante nei casi in cui lo stato della sessione viene salvato in locale sul server per una sessione utente.

## <a name="possible-problem-causes"></a>Possibili cause del problema

Il problema nel mantenimento dell'affinità di sessione basata su cookie può verificarsi a causa dei seguenti motivi principali:

- L'impostazione "Affinità basata su cookie" non è abilitata
- L'applicazione non è in grado di gestire l'affinità basata su cookie
- L'applicazione utilizza affinità basata su cookie, ma le richieste continuano a rimbalzare tra server back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verificare se l'impostazione "Affinità basata su cookie" è abilitata

A volte i problemi di affinità di sessione possono verificarsi quando si dimentica di abilitare l'impostazione "Affinità basata su cookie". Per determinare se è stata abilitata l'impostazione "Affinità basata su cookie" nella scheda Impostazioni HTTP nel portale di Azure, seguire le istruzioni:To determine whether you have enabled the "Cookie based affinity" setting on the HTTP Settings tab in the Azure portal, follow the instructions:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nel riquadro **di spostamento sinistro** fare clic su Tutte le **risorse**. Fare clic sul nome del gateway applicazione nel pannello Tutte le risorse. Se la sottoscrizione selezionata contiene già diverse risorse, è possibile immettere il nome del gateway applicazione nella casella **Filtra per nome...** per accedere con facilità al gateway applicazione.

3. Selezionare la scheda **Impostazioni HTTP** in **IMPOSTAZIONI**.

   ![risoluzione dei problemi di sessione-affinità-problemi-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Fare clic su **appGatewayBackendHttpSettings** sul lato destro per verificare se è stata selezionata l'opzione Abilitato per l'affinità **basata** su cookie.

   ![risoluzione dei problemi di sessione-affinità-problemi-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



È inoltre possibile controllare il valore di "**CookieBasedAffinity**" è impostato su *Enabled*in "**backendHttpSettingsCollection**" utilizzando uno dei seguenti metodi:

- Eseguire [Get-AzApplicationGatewayBackendHttpSetting in PowerShellRun Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) in PowerShell
- Esaminare il file JSON usando il modello di Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>L'applicazione non è in grado di gestire l'affinità basata su cookie

#### <a name="cause"></a>Causa

Il gateway applicazione può eseguire l'affinità basata sulla sessione solo utilizzando un cookie.

#### <a name="workaround"></a>Soluzione alternativa

Se l'applicazione non è in grado di gestire l'affinità basata su cookie, è necessario usare un servizio di bilanciamento del carico di Azure esterno o interno o un'altra soluzione di terze parti.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>L'applicazione utilizza affinità basata su cookie, ma le richieste continuano a rimbalzare tra server back-end

#### <a name="symptom"></a>Sintomo

È stata abilitata l'impostazione Affinità basata su cookie, quando si accede al [http://website](http://website/) gateway applicazione utilizzando un URL di nome breve in Internet Explorer, ad esempio: , la richiesta continua a rimbalzare tra i server back-end.

Per identificare questo problema, seguire le istruzioni:

1. Prendere una traccia del debugger web sul "Client" che si connette all'applicazione dietro il gateway applicazione (Stiamo usando Fiddler in questo esempio).
    **Suggerimento** Se non sai come usare il Fiddler, seleziona l'opzione " Voglio raccogliere il traffico di**rete e analizzarlo utilizzando**il debugger web " nella parte inferiore.

2. Controllare e analizzare i registri di sessione, per determinare se i cookie forniti dal client hanno i dettagli ARRAffinity. Se non si trovano i dettagli di ARRAffinity, ad esempio "**ARRAffinity "** *ARRAffinityValue*" all'interno del set di cookie, significa che il client non risponde con il cookie ARRA, fornito dal gateway applicazione.
    Ad esempio:

    ![risoluzione dei problemi di sessione-affinità-problemi-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![risoluzione dei problemi relativi all'affinità di sessione-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

L'applicazione continua a tentare di impostare il cookie su ogni richiesta fino a quando non ottiene risposta.

#### <a name="cause"></a>Causa

Questo problema si verifica perché Internet Explorer e altri browser potrebbero non memorizzare o utilizzare il cookie con un URL di nome breve.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema è necessario accedere al gateway applicazione usando un nome di dominio completo. Ad esempio, [http://website.com](https://website.com/) [http://appgw.website.com](http://appgw.website.com/) utilizzare o .

## <a name="additional-logs-to-troubleshoot"></a>Registri aggiuntivi per la risoluzione dei problemi

È possibile raccogliere log aggiuntivi e analizzarli per risolvere i problemi relativi all'affinità di sessione basata su cookie

### <a name="analyze-application-gateway-logs"></a>Analizzare i log del gateway applicazioneAnalyze Application Gateway logs

Per raccogliere i log del gateway applicazione, seguire le istruzioni:

Abilitare la registrazione tramite il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/)individuare la risorsa e quindi fare clic su **Log di diagnostica**.

   Per il gateway applicazione sono disponibili tre registri: Registro di accesso, Registro prestazioni, Registro firewall

2. Per iniziare a raccogliere dati, fare clic su **Attiva diagnostica**.

   ![risoluzione dei problemi di sessione-affinità-problemi-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Il pannello **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Fare clic su **Configura** in **Log Analytics** per impostare l'area di lavoro. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![risoluzione dei problemi di sessione-affinità-problemi-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confermare le impostazioni e quindi fare clic su **Salva**.

   ![risoluzione dei problemi di sessione-affinità-problemi-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visualizzare e analizzare i log di accesso al gateway applicazione

1. Nel portale di Azure nella visualizzazione delle risorse del gateway applicazione selezionare Log di **diagnostica** nella sezione **MONITORAGGIO.**

   ![risoluzione dei problemi di sessione-affinità-problemi-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Sul lato destro selezionare "**ApplicationGatewayAccessLog**" nell'elenco a discesa in **Categorie di log.**  

   ![risoluzione dei problemi di sessione-affinità-problemi-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Nell'elenco Registro di accesso gateway applicazione fare clic sul log che si desidera analizzare ed esportare e quindi esportare il file JSON.

4. Convertire il file JSON esportato nel passaggio 3 in un file CSV e visualizzarlo in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati.

5. Controllare i seguenti dati:

- **ClientIP:** indirizzo IP del client che si connette.
- **ClientPort** - Questa è la porta di origine dal client di connessione per la richiesta.
- **RequestQuery:** indica il server di destinazione che la richiesta viene ricevuta.
- **Server-Routed:** istanza del pool back-end che la richiesta viene ricevuta.
- **X-AzureApplicationGateway-LOG-ID**: ID correlazione usato per la richiesta. Può essere usato per risolvere i problemi di traffico nei server back-end. Ad esempio: X-AzureApplicationGateway-CACHE-HIT-0&SERVER-ROUTED-10.0.2.4.

  - **SERVER-STATUS**: codice di risposta HTTP ricevuto dal gateway applicazione dal back-end.

  ![risoluzione dei problemi di sessione-affinità-problemi-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vengono visualizzati due elementi provenienti dallo stesso ClientIP e dalla stessa porta client e vengono inviati allo stesso server back-end, significa che il gateway applicazione è stato configurato correttamente.

Se vengono visualizzati due elementi provenienti dallo stesso ClientIP e dalla stessa porta client e vengono inviati ai diversi server back-end, significa che la richiesta rimbalza tra i server back-end, selezionare **"L'applicazione utilizza affinità basata su cookie ma le richieste continuano a rimbalzare tra i server back-end**" nella parte inferiore per risolverla.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Utilizzare il debugger Web per acquisire e analizzare i traffici HTTP o HTTPS

Strumenti di debug Web come Fiddler, consentono di eseguire il debug di applicazioni web acquisendo il traffico di rete tra Internet e computer di prova. Questi strumenti consentono di controllare i dati in entrata e in uscita man mano che il browser li riceve/li invia. Fiddler, in questo esempio, ha l'opzione di riproduzione HTTP che consente di risolvere i problemi lato client con le applicazioni web, in particolare per il tipo di problema di autenticazione.

Utilizzare il debugger Web di propria scelta. In questo esempio useremo Fiddler per acquisire e analizzare i traffici http o https, seguire le istruzioni:

1. Scaricare lo strumento Fiddler all'indirizzo <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Scegliere Fiddler4 se nel computer di acquisizione è installato .NET 4. In caso contrario, scegliere Fiddler2.Otherwise, choose Fiddler2.

2. Fare clic con il pulsante destro del mouse sull'eseguibile di installazione ed eseguire come amministratore per l'installazione.

    ![risoluzione dei problemi di sessione-affinità-problemi-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando apri Fiddler, dovrebbe iniziare automaticamente a catturare il traffico (notare l'acquisizione nell'angolo in basso a sinistra). Premere F12 per avviare o arrestare l'acquisizione del traffico.

    ![risoluzione dei problemi di sessione-affinità-problemi-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Molto probabilmente, si sarà interessati a decrittografare il traffico HTTPS ed è possibile abilitare la decrittografia HTTPS selezionando **Strumenti** > **Opzioni Fiddler**e selezionare la casella " **Decrittografa traffico HTTPS**".

    ![risoluzione dei problemi di sessione-affinità-problemi-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. È possibile rimuovere le sessioni non correlate precedenti prima di riprodurre il problema facendo clic su **X** (icona) > **Rimuovi tutto** come segue screenshot: 

    ![risoluzione dei problemi di sessione-affinità-problemi-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Dopo aver riprodotto il problema, salvare il **File** > file per la revisione selezionando**Salva** > **tutte le sessioni..**. 

    ![risoluzione dei problemi di sessione-affinità-problemi-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Controllare e analizzare i registri di sessione per determinare il problema.

    Ad esempio:

- **Esempio A:** Si trova un registro di sessione che la richiesta viene inviata dal client e va all'indirizzo IP pubblico del gateway applicazione, fare clic su questo registro per visualizzare i dettagli.  Sul lato destro, i dati nella casella inferiore sono ciò che il gateway applicazione sta tornando al client. Selezionare la scheda "RAW" e determinare se il client sta ricevendo un "**Set-Cookie: ARRAffinity** *ABAffinityValue*". Se non è presente alcun cookie, l'affinità di sessione non è impostata o il gateway applicazione non applica nuovamente i cookie al client.

   > [!NOTE]
   > Questo valore ARRAffinity è l'id del cookie, che il gateway applicazione imposta per il client da inviare a un determinato server back-end.

   ![risoluzione dei problemi di sessione-affinità-problemi-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Esempio B:** Il log di sessione successivo seguito dal precedente è il client che risponde al gateway applicazione, che ha impostato ARRAAFFINITY. Se l'ID cookie ARRAffinity corrisponde, il pacchetto deve essere inviato allo stesso server back-end utilizzato in precedenza. Controllare le seguenti righe di comunicazione http successiva per verificare se il cookie ARRAffinity del client sta cambiando.

   ![risoluzione dei problemi di sessione-affinità-problemi-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Per la stessa sessione di comunicazione, il cookie non deve essere modificato. Seleziona la casella in alto sul lato destro, seleziona la scheda "Cookie" per vedere se il client sta utilizzando il cookie e lo invia di nuovo al gateway applicazione. In caso contrario, il browser client non mantiene e utilizza il cookie per le conversazioni. A volte, il cliente potrebbe mentire.

 

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
