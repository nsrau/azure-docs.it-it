<properties
   pageTitle="Novità di Power BI Embedded"
   description="Informazioni più recenti sulle novità di Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/06/2016"
   ms.author="owend"/>

# Novità di Power BI Embedded

Gli aggiornamenti su **Power BI Embedded** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includono tuttavia nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. In questa pagina vengono evidenziate nuove funzionalità destinate all'utente. È consigliabile controllarla spesso.

## 11 luglio 2016

Contenuti di questa versione:

-    **Novità** Il servizio Power BI Embedded non è più disponibile in anteprima: è ora disponibile a livello generale.
-    Tutte le API REST sono state spostate da **/beta** a **/v1.0**.
-    Gli SDK per .NET e JavaScript sono stati aggiornati per la versione **v1.0**.
-    Le chiamate all'API di Power BI possono essere ora autenticate direttamente mediante le chiavi API. I token dell'app sono necessari solo per l'incorporamento. I token di provisioning e di sviluppo sono stati quindi deprecati nelle API della versione 1.0 , ma continueranno a funzionare nella versione Beta fino al 30/12/2016. Per altre informazioni, vedere [Informazioni sul flusso dei token delle app in Power BI Embedded](power-bi-embedded-app-token-flow.md).
-    Supporto della sicurezza a livello di riga per token dell'app e report incorporati. Per altre informazioni, vedere [Row level security with Power BI Embedded](power-bi-embedded-rls.md) (Sicurezza a livello di riga con Power BI Embedded).
-    Applicazione di esempio aggiornata per tutte le chiamate all'API **v1.0**.
-    Supporto di Power BI Embedded per Azure SDK, PowerShell e interfaccia della riga di comando.
-    Gli utenti possono esportare i dati di visualizzazione in un file con estensione **csv**.
-    Power BI Embedded è ora supportato in tutte le lingue/impostazioni locali di Microsoft Azure. Per altre informazioni, vedere [Azure - Languages](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx) (Azure - Lingue).

<!---HONumber=AcomDC_0713_2016-->