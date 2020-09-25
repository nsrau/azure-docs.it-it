---
title: Pianificare l'uso della soluzione per SMS e telefonia di Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni su come pianificare in modo efficace l'uso dei numeri di telefono e della telefonia.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944243"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Pianificare l'uso della soluzione di telefonia e SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Questo documento descrive i diversi piani di telefonia e i tipi di numeri offerti da Servizi di comunicazione di Azure. Vengono illustrati i flussi decisionali che consentono di selezionare un provider di servizi vocali, i tipi di numero di telefono, i piani e le funzionalità disponibili tramite Servizi di comunicazione.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Informazioni sui numeri di telefono disponibili in Servizi di comunicazione di Azure

Servizi di comunicazione di Azure consente di usare i numeri di telefono per inviare e ricevere SMS e chiamate telefoniche. Questi numeri di telefono possono essere usati per configurare l'ID chiamante per le chiamate in uscita effettuate tramite il servizio.
  
Se non si dispone di un numero di telefono esistente da importare nella soluzione Servizi di comunicazione, il modo più semplice per iniziare consiste nell'ottenere un nuovo numero di telefono da Servizi di comunicazione di Azure in pochi minuti.

Se si dispone di un numero di telefono esistente che si vuole continuare a usare nella soluzione (ad esempio, 1-800-azienda), è possibile trasferire il numero di telefono dal provider esistente a Servizi di comunicazione.

Il diagramma seguente illustra le opzioni disponibili:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagramma che illustra il flusso decisionale relativo alla scelta dei numeri di telefono.":::

Di seguito vengono esaminati i tipi di numero di telefono e le funzionalità disponibili tramite Servizi di comunicazione. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Offerta diretta Microsoft per numeri di telefono e funzionalità

Servizi di comunicazione di Azure offre flessibilità eccellente per gli sviluppatori. Per la maggior parte dei numeri di telefono, è possibile configurare un set di piani "à la carte". Alcuni sviluppatori necessitano solo di un piano per le chiamate in entrata, altri potrebbero aver bisogno di piani SMS in entrata e in uscita. Questi piani possono essere selezionati al momento del lease e/o del trasferimento dei numeri di telefono in Servizi di comunicazione.

I piani disponibili variano in base al paese e al tipo di numero di telefono con cui si sta operando. Il diagramma seguente illustra il flusso decisionale:    I piani disponibili variano in base al paese e al tipo di numero di telefono con cui si sta operando.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

Prima di selezionare un tipo di numero di telefono, è utile rivedere il piano di numerazione internazionale.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Lettura facoltativa. Piano di numerazione delle telecomunicazioni pubbliche internazionali (E.164)

> [!NOTE]
> Per comprendere meglio i tipi di numeri e le funzionalità proposti dall'offerta diretta dei Servizi di comunicazione Azure, è consigliabile esaminare queste informazioni anche se si ha già familiarità con il piano di numerazione delle telecomunicazioni pubbliche internazionali E.164.

Il piano di numerazione delle telecomunicazioni pubbliche internazionali è definito nella raccomandazione ITU (International Telecommunication Union) E. 164. I numeri conformi possono contenere un massimo di 15 cifre.

Il numero di telefono è costituito da questi elementi:

-   Un prefisso "+"
-   Un prefisso internazionale o codice paese/area geografica (a una, due o tre cifre) 
-   *(Facoltativo)* Un codice di destinazione nazionale o piano di numerazione, comunemente definito prefisso. La lunghezza di questo codice dipende dal paese. Negli Stati Uniti, il codice è composto da tre cifre. In Australia e in Nuova Zelanda da una cifra. Germania, Giappone, Messico e altri paesi hanno prefissi con lunghezza variabile. In Germania, ad esempio, il codice area può essere composto da due a cinque cifre, in Giappone da una a cinque cifre.
-   Un numero abbonato

> [!NOTE]
> La classificazione precedente non è completamente conforme allo standard ITU E.164 e ha lo scopo di fornire una descrizione semplificata. Il numero del sottoscrittore, ad esempio, è suddiviso all'interno dello standard. Per informazioni più approfondite sul piano di numerazione internazionale, lo [standard ITU E.164](https://www.itu.int/rec/T-REC-E.164) è un ottimo punto di partenza.  

Di seguito sono riportati alcuni esempi che consentono di comprendere meglio il piano di numerazione:

Numero di telefono regionale negli Stati Uniti:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Esempio di numero di telefono regionale negli Stati Uniti":::

Numero di telefono regionale in Canada:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Esempio di numero di telefono regionale in Canada":::

Numero verde in 'America del Nord:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Esempio di numero verde in America del Nord":::

Numero di telefono cellulare nel Regno Unito:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Esempio di numero cellulare nel Regno Unito":::

Di seguito vengono illustrati i tipi di numero di telefono specifici disponibili in Servizi di comunicazione di Azure.

## <a name="phone-number-types-in-azure-communication-services"></a>Tipi di numeri di telefono in Servizi di comunicazione di Azure

Microsoft offre piani regionali e piani per i numeri verdi gratuiti per codici brevi e numeri di cellulare nei paesi ove applicabile.

La tabella seguente riepiloga i tipi di numeri di telefono disponibili: 

| Tipo di numero di telefono | Esempio                              | Paesi di disponibilità    | Caso d'uso comune                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regionale          | \+ 1 (codice area geografica) XXX XX XX  | Stati Uniti, Canada, Puerto Rico | Assegnazione dei numeri di telefono agli utenti nelle applicazioni o assegnazione a bot/sistemi IVR (Interactive Voice Response) |
| Numero verde         | \+ 1 (*codice* area n. verde) XXX XX XX | Stati Uniti, Canada, Puerto Rico | Assegnazione a bot/sistemi IVR (Interactive Voice Response), applicazioni SMS                                        |

## <a name="plans"></a>Plans 

Di seguito sono elencate le funzionalità che è possibile abilitare per i numeri di telefono. Le funzionalità variano in base al paese a causa dei requisiti normativi. Servizi di comunicazione di Azure offre le funzionalità seguenti:

- **SMS in uscita unidirezionali**, utile per gli scenari di notifica e autenticazione a due fattori.
- **SMS in entrata e in uscita bidirezionali**, un pacchetto predefinito che consente l'invio e la ricezione di SMS nell'ambito del piano.
- **Chiamata PSTN**, è possibile selezionare una chiamata in entrata e usare l'ID chiamante per effettuare le chiamate in uscita.

## <a name="next-steps"></a>Passaggi successivi

### <a name="quickstarts"></a>Guide rapide introduttive

- [Ottenere un numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md)
- [Effettuare una chiamata](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Inviare un SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentazione concettuale

- [Concetti relativi alle chiamate vocali e video](../voice-video-calling/about-call-types.md)
- [Flussi di chiamate e flussi SMS](../call-flows.md)
- [Prezzi](../pricing.md)
