---
title: Risolvere gli errori di connessione del cluster di Azure Data ExplorerTroubleshoot Azure Data Explorer cluster connection failures
description: Questo articolo descrive la procedura di risoluzione dei problemi per la connessione a un cluster in Azure Data Explorer.This article describes troubleshooting steps for connecting to a cluster in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827037"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Risoluzione dei problemi: Errore durante la connessione a un cluster in Esplora dati di Azure

Se non si riesce a connettersi a un cluster in Esplora dati di Azure, seguire questa procedura.

1. Assicurarsi che la stringa di connessione sia corretta. Deve essere nel formato: `https://<ClusterName>.<Region>.kusto.windows.net`, come nell'esempio seguente: `https://docscluster.westus.kusto.windows.net`.

1. Assicurarsi di disporre delle autorizzazioni appropriate. In caso contrario, si otterrà una risposta di tipo *non autorizzato*.

    Per altre informazioni sulle autorizzazioni, vedere [Gestire le autorizzazioni per i database](manage-database-permissions.md). Se necessario, rivolgersi all'amministratore del cluster per essere aggiunti al ruolo appropriato.

1. Assicurarsi che il cluster non sia stato eliminato: rivedere il log attività nella sottoscrizione.

1. Controllare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/). Cercare lo stato di Esplora dati di Azure nell'area in cui si sta cercando di connettersi al cluster.

    Se lo stato non è **Buono** (segno di spunta verde), provare a connettersi al cluster dopo che lo stato è migliorato.

1. Per richiedere ulteriore assistenza per la risoluzione del problema, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).