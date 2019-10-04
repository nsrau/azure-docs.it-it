---
title: "Procedura: apertura delle porte del firewall necessarie per un'applicazione Proxy di applicazione | Microsoft Docs"
description: Individuare le porte da aprire per fare in modo che il proxy dell'applicazione Azure Active Directory funzioni correttamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e69f2e5049ca290a17c058c9d18dc7c6ec91f49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783571"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Procedura: apertura delle porte del firewall necessarie per un'applicazione Proxy di applicazione

Per visualizzare un elenco completo delle porte necessarie e la funzione di ciascuna porta, vedere la sezione dei prerequisiti della [documentazione del Proxy di applicazione](application-proxy-add-on-premises-application.md). Si noti che il Proxy di applicazione usa solo le porte in uscita.

È anche possibile verificare se si dispone di tutte le porte necessarie per accedere, aprire il [strumento di Test delle porte connettore](https://aadap-portcheck.connectorporttest.msappproxy.net/) dalla rete locale. La presenza di più segni di spunta verdi indica una maggiore resilienza. 

## <a name="app-proxy-regions"></a>Aree del proxy dell'applicazione

In futuro sarà possibile specificare quali aree devono essere verdi per l'utente. Per il momento, assicurarsi che tutte siano verdi. Anche l'area Stati Uniti centrali è necessaria indipendentemente dall'area in cui ci si trova.

Per assicurarsi che lo strumento fornisca i risultati corretti, accertarsi di:

-   Aprire lo strumento in un browser dal server in cui è installato il connettore.

-   Assicurarsi che qualsiasi proxy o firewall applicabile per il connettore venga applicato anche a questa pagina. Questa operazione può essere eseguita in Internet Explorer selezionando **le impostazioni**  - &gt; **Opzioni Internet**  - &gt; **connessioni**  - &gt; **Impostazioni LAN**. In questa pagina viene visualizzato il campo "Usa un server di proxy per la rete LAN". Selezionare questa casella e inserire l'indirizzo del proxy nel campo "Indirizzo".

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
