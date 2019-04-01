---
title: Errore durante la creazione di un cluster di Esplora dati di Azure
description: Questo articolo descrive i passaggi di risoluzione dei problemi per la creazione di un cluster in Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b95dabbdecd98902da3bf8217a14f41019c31e82
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757685"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Risoluzione dei problemi: Errore durante la creazione di un cluster di Esplora dati di Azure

Nel caso improbabile che si verifichi un errore di creazione del cluster in Esplora dati di Azure, seguire questa procedura.

1. Assicurarsi di disporre delle autorizzazioni appropriate. Per creare un cluster è necessario essere un membro del ruolo *Collaboratore* oppure *Proprietario* della sottoscrizione di Azure. Se necessario rivolgersi all'amministratore della sottoscrizione per essere aggiunti al ruolo appropriato.

1. Assicurarsi che non ci siano errori di validazione relativi al nome del cluster inserito in **Creare cluster** nel portale di Azure.

1. Controllare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/). Cercare lo stato di Esplora dati di Azure nell'area in cui si sta cercando di creare il cluster.

    Se lo stato non è **Buono** (segno di spunta verde), provare a creare il cluster una volta avvenuto un miglioramento dello stato.

1. Per richiedere ulteriore assistenza per la risoluzione del problema, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).