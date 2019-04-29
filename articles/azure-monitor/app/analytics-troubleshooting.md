---
title: Risolvere i problemi di Analytics in Azure Application Insights | Documentazione Microsoft
description: 'Problemi con Application Insights Analytics? Inizia da qui. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692382"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Risoluzione dei problemi di Analytics in Application Insights
Problemi con [Application Insights Analytics](analytics.md)? Inizia da qui. Analytics è l'efficace strumento di ricerca incluso in Application Insights di Azure.

## <a name="limits"></a>Limiti
* Attualmente sono disponibili i risultati delle query per una settimana di dati.
* Browser testati: versioni più recenti di Chrome, Microsoft Edge e Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Estensioni del browser dall'incompatibilità nota
* Ghostery

Disabilitare l'estensione o utilizzare un altro browser.

## <a name="e-a"></a> "Errore imprevisto"
![Schermata di errore imprevisto](media/analytics-troubleshooting/010.png)

Si è verificato un errore interno durante il runtime del portale: eccezione non gestita.

* Svuotare la cache del browser.

## <a name="e-b"></a>403 ... provare a ricaricare la pagina
![403 ... please try to reload](media/analytics-troubleshooting/020.png)

Si è verificato un errore correlato all'autenticazione (durante l'autenticazione o durante la generazione del token di accesso). Potrebbe non essere possibile ripristinare il portale senza modificare le impostazioni del browser.

* Verificare che nel browser siano attivati i [cookie di terze parti](#cookies) . 

## <a name="authentication"></a>403 ... verificare l'area di sicurezza
![403 ... verify security zone](media/analytics-troubleshooting/030.png)

Si è verificato un errore correlato all'autenticazione (durante l'autenticazione o durante la generazione del token di accesso). Potrebbe non essere possibile ripristinare il portale senza modificare le impostazioni del browser.

1. Verificare che nel browser siano attivati i [cookie di terze parti](#cookies) . 
2. Il portale Analytics è stato aperto utilizzando preferiti, segnalibri o un collegamento salvato? L'accesso è stato eseguito con credenziali diverse da quelle utilizzate quando è stato salvato il collegamento?
3. Provare a utilizzare una finestra del browser privata/in incognito (dopo aver chiuso tutte le finestre di questo tipo). Sarà necessario fornire le credenziali. 
4. Aprire un'altra finestra del browser (normale) e accedere ad [Azure](https://portal.azure.com). Uscire, quindi aprire il collegamento ed effettuare l'accesso con le credenziali corrette.
5. Gli utenti di Microsoft Edge e Internet Explorer possono ricevere questo errore anche quando le impostazioni dell'area attendibile non sono supportate.
   
    Verificare che il [portale di Analytics](https://portal.azure.com) e il [portale di Azure Active Directory](https://portal.azure.com) si trovino nella stessa area di sicurezza:
   
   * In Internet Explorer aprire **Opzioni Internet**, **Sicurezza**, **Siti attendibili**, **Siti**:
     
     ![Finestra di dialogo Opzioni Internet, aggiungere un sito all'elenco dei siti attendibili](media/analytics-troubleshooting/033.png)
     
     Se nell'elenco dei siti Web compaiono i seguenti URL, controllare che siano inclusi anche gli altri:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Resource not found
![404 ... resource not found](media/analytics-troubleshooting/040.png)

Una risorsa dell'applicazione è stata eliminata da Application Insights e non è più disponibile. Questa situazione può verificarsi se è stato salvato l'URL alla pagina di Analytics.

## <a name="e-e"></a>403 ... No authorization
![403 ... not authorized](media/analytics-troubleshooting/050.png)

Non si dispone dell'autorizzazione ad aprire questa applicazione in Analytics.

* Se il collegamento è stato fornito da un altro utente, Chiedere la conferma che l'utente destinatario sia un [lettore o collaboratore di questo gruppo di risorse](../../azure-monitor/app/resources-roles-access-control.md).
* Se il collegamento è stato salvato con credenziali diverse, Aprire il [portale di Azure](https://portal.azure.com), disconnettersi e riprovare a usare questo collegamento, inserendo le credenziali corrette.

## <a name="html-storage"></a>403 ... HTML5 Storage
Il portale utilizza HTML5 localStorage e sessionStorage.

* Chrome: Impostazioni, Privacy, Impostazioni contenuti.
* Internet Explorer: Opzioni Internet, scheda Avanzate, Sicurezza, Abilita archiviazione DOM

![403 ... try to enable HTML5 storage](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Sottoscrizione non trovata
![404 ... Sottoscrizione non trovata](media/analytics-troubleshooting/070.png)

L'URL non è valido. 

* Aprire la risorsa dell'app nel [portale di Application Insights](https://portal.azure.com), quindi utilizzare il pulsante di analisi.

## <a name="e-h"></a>404 ... la pagina non esiste
![404 ... Page does not exist](media/analytics-troubleshooting/080.png)

L'URL non è valido.

* Aprire la risorsa dell'app nel [portale di Application Insights](https://portal.azure.com), quindi utilizzare il pulsante di analisi.

## <a name="cookies"></a>Abilitare i cookie di terze parti
  Vedere [come disabilitare il cookie di terze parti](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), tenendo conto che in questo caso è necessario **abilitarli** .


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

