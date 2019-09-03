---
title: Informazioni su OPC Vault - Azure | Microsoft Docs
description: Panoramica di OPC Vault
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 44315790116545dd888aed533731bbf01abe801d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997300"
---
# <a name="what-is-opc-vault"></a>Informazioni su OPC Vault

OPC Vault è un microservizio che consente di configurare, registrare e gestire il ciclo di vita dei certificati per le applicazioni server e client OPC UA nel cloud. Questo articolo descrive i semplici casi d'uso di OPC Vault.

## <a name="certificate-management"></a>Gestione dei certificati

Ad esempio, un'azienda manifatturiera deve connettere il proprio server OPC UA a una nuova applicazione client sviluppata. Quando viene eseguito l'accesso iniziale del server, nell'applicazione server OPC UA viene immediatamente visualizzato un messaggio di errore, a indicare che l'applicazione client non è sicura. Questo meccanismo è integrato nel server OPC UA per impedire l'accesso non autorizzato all'applicazione, evitando attacchi di hacker nella fabbrica.

## <a name="application-security-management"></a>Gestione della sicurezza delle applicazioni
Un professionista della sicurezza usa il microservizio OPC Vault per consentire al server OPC UA di comunicare facilmente con qualsiasi applicazione client, perché OPC Vault include tutte le funzioni per la registrazione, l'archiviazione e la gestione del ciclo di vita dei certificati. Una volta connesso in modo sicuro, il server OPC UA può comunicare con la nuova applicazione client sviluppata.

## <a name="the-complete-opc-vault-architecture"></a>Architettura OPC Vault completa
Il diagramma seguente illustra l'architettura OPC Vault completa.

![Architettura OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso cosa è OPC Vault e come si usa, ecco il prossimo passaggio suggerito:

> [!div class="nextstepaction"]
> [Architettura di OPC Vault](overview-opc-vault-architecture.md)
