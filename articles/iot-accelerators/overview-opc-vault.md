---
title: Informazioni su OPC Vault - Azure | Microsoft Docs
description: Questo articolo offre una panoramica di OPC Vault. Consente di configurare, registrare e gestire il ciclo di vita dei certificati per applicazioni OPC UA nel cloud.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826207"
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
