---
title: Risolvere i problemi di download dei dettagli di utilizzo per le prenotazioni di Azure
description: Questo articolo illustra come risolvere il problema per cui il download dei dettagli di utilizzo delle istanze riservate non è disponibile nel portale di Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147327"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Risolvere i problemi di download dei dettagli di utilizzo per le prenotazioni di Azure

Questo articolo illustra come risolvere il problema per cui il download dei dettagli di utilizzo delle istanze riservate non è disponibile nel portale di Azure.

## <a name="symptoms"></a>Sintomi

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare a **Prenotazioni**.
1. Selezionare una prenotazione.
1. Nella pagina di panoramica della prenotazione la visualizzazione predefinita mostra l'utilizzo per gli ultimi sette giorni. È possibile selezionare **Scarica come CSV** per scaricare i dettagli di utilizzo della prenotazione.
1. Se si cambia l'intervallo di tempo, l'opzione **Scarica come CSV** diventa non disponibile.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Esempio che mostra l'opzione Scarica come CSV non disponibile" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Causa

A causa di limitazioni tecniche, Azure non supporta il download dei dati per un periodo più lungo di sette giorni. I periodi lunghi per i clienti con grandi quantità di prenotazioni generano grandi quantità di dati. La restituzione di dati tramite API comporta un sovraccarico per le risorse di Azure.

## <a name="solution"></a>Soluzione

È comprensibile che i clienti vogliano scaricare dati per periodi più lunghi. Tuttavia, attualmente non è possibile scaricare i dati di utilizzo delle prenotazioni per lunghi periodi.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni, vedere [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md).