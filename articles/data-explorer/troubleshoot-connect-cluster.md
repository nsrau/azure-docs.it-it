---
title: Errore durante la connessione a un cluster in Esplora dati di Azure
description: Questo articolo descrive i passaggi per la risoluzione dei problemi relatici alla connessione a un cluster in Esplora dati di Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d10d39a65acd3664c99e8b5aa5cc015a76d9d1aa
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209378"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Risoluzione dei problemi: Errore durante la connessione a un cluster in Esplora dati di Azure

Se non si riesce a connettersi a un cluster in Esplora dati di Azure, seguire questa procedura.

1. Assicurarsi che la stringa di connessione sia corretta. Deve essere nel formato: `https://<ClusterName>.<Region>.kusto.windows.net`, come nell'esempio seguente: `https://docscluster.westus.kusto.windows.net`.

1. Assicurarsi di disporre delle autorizzazioni appropriate. In caso contrario, si otterrà una risposta di tipo *non autorizzato*.

    Per altre informazioni sulle autorizzazioni, vedere [Gestire le autorizzazioni per i database](manage-database-permissions.md). Se necessario, rivolgersi all'amministratore del cluster per essere aggiunti al ruolo appropriato.

1. Assicurarsi che il cluster non sia stato eliminato: rivedere il log attività nella sottoscrizione.

1. Controllare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/>). Cercare lo stato di Esplora dati di Azure nell'area in cui si sta cercando di connettersi al cluster.

    Se lo stato è diverso da **Buono**, valore indicato dal segno di spunta verde, provare a connettersi al cluster dopo che lo stato è migliorato.

1. Per richiedere ulteriore assistenza per la risoluzione del problema, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).