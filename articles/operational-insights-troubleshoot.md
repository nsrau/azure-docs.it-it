<properties 
   pageTitle="Risolvere i problemi con Operational Insights"
   description="Informazioni sulla risoluzione dei problemi con Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

#Risolvere i problemi con Operational Insights
Per semplificare la risoluzione dei problemi, è possibile usare le informazioni disponibili nelle sezioni seguenti. Se il problema specifico non è incluso in questo articolo, vedere il [blog del team di Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnosticare problemi di connessione per Operational Insights

Poiché Microsoft Azure Operational Insights si basa sui dati spostati verso il cloud e dal cloud, i problemi di connessione possono causare un blocco del servizio. Usare le seguenti informazioni per comprendere e risolvere i problemi di connessione.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Messaggio di errore</b></td>
		<td><b>Possibili cause</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>La connettività Internet è stata verificata, ma non è possibile stabilire la connessione al servizio Operational Insights. Riprovare più tardi.|</td>
		<td>Il servizio Operational Insights è in fase di manutenzione. Attendere il completamento della manutenzione di Operational Insights.<p>La rete ha bloccato Operational Insights. Contattare l'amministratore di rete e richiedere l'accesso a Operational Insights oppure usare un altro server come gateway.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Non è possibile stabilire una connessione Internet. Controllare le impostazioni del proxy.</td>
		<td>Il server non è connesso a Internet. Controllare lo stato della connettività Internet e connettere il server a Internet.<p>L'impostazione del proxy non è corretta. Per informazioni su come configurare o modificare le impostazioni del proxy, vedere <A HREF="operational-insights-proxy-filewall.md">Configurare le impostazioni di proxy e firewall</A>.<p>Il server proxy richiede l'autenticazione. Per informazioni su come configurare Operations Manager per l'uso di un server proxy, vedere <A HREF="operational-insights-proxy-filewall.md">Configurare le impostazioni di proxy e firewall</A>.</td>
    </tr>
    </tbody>
    </table>

## Risolvere i problemi di individuazione di SQL Server

Se si esegue Microsoft SQL Server 2008 R2 e, nonostante sia stato distribuito l'agente Operations Manager, non vengono visualizzati gli avvisi relativi a questo server, potrebbe essersi verificato un problema di individuazione.

Per verificare se questa è l'origine del problema, controllare se sono presenti i seguenti problemi:

- Nel log eventi di Operations Manager viene visualizzato l'ID evento 4001. Questo evento indica la presenza di una classe non valida.

- Quando in Gestione configurazione SQL Server si visualizzano i servizi SQL Server, appare il messaggio di errore "La chiamata di procedura remota non è riuscita. [0x0800706be]"

Se sono presenti entrambi i problemi, sarà necessario installare SQL Server 2008 R2 Service Pack 2. Per scaricare questo Service Pack, vedere [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) nell'Area download Microsoft.

Dopo l'installazione del Service Pack, i dati di Operational Insights per il server dovrebbero essere visualizzati entro 24 ore.



<!--HONumber=52-->