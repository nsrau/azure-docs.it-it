---
title: Azure Key Vault lo trasferimento di un insieme di credenziali in un gruppo di risorse diverso | Microsoft Docs
description: Linee guida per lo stato di un insieme di credenziali delle chiavi in un gruppo di risorse diverso.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: fe8051d551077666c06ac033f22303fd643ac602
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585734"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Trasferimento di una Azure Key Vault tra gruppi di risorse

## <a name="overview"></a>Panoramica

Lo stato di un insieme di credenziali delle chiavi tra gruppi di risorse è una funzionalità di Key Vault supportata. Lo stato di un insieme di credenziali delle chiavi tra gruppi di risorse non influirà sul firewall di Key Vault o sulle configurazioni dei criteri di accesso Le applicazioni connesse e le entità servizio devono continuare a funzionare come previsto.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

È possibile che l'organizzazione abbia implementato criteri di Azure con l'imposizione o le esclusioni a livello di gruppo di risorse. Nel gruppo di risorse in cui è attualmente presente l'insieme di credenziali delle chiavi e nel gruppo di risorse in cui si sta muovendo l'insieme di credenziali delle chiavi potrebbe essere presente un set di assegnazioni di criteri diverso. Un conflitto nei requisiti dei criteri può causare l'interruzione delle applicazioni.

### <a name="example"></a>Esempio

Si dispone di un'applicazione connessa a Key Vault che crea certificati validi per due anni. Il gruppo di risorse in cui si tenta di spostare l'insieme di credenziali delle chiavi è associato a un'assegnazione di criteri che blocca la creazione di certificati validi per più di un anno. Dopo aver spostato l'insieme di credenziali delle chiavi nel nuovo gruppo di risorse, l'operazione di creazione di un certificato valido per due anni verrà bloccata da un'assegnazione di criteri di Azure.

### <a name="solution"></a>Soluzione

Assicurarsi di passare alla pagina Criteri di Azure nella portale di Azure ed esaminare le assegnazioni dei criteri per il gruppo di risorse corrente, nonché il gruppo di risorse in cui si sta effettuando il passaggio e assicurarsi che non ci siano mancate corrispondenze.

## <a name="procedure"></a>Procedura

1. Accedere al Portale di Azure
2. Passare a Key Vault
3. Fare clic sulla scheda "panoramica"
4. Selezionare il pulsante "Sposta"
5. Selezionare "sposta in un altro gruppo di risorse" dalle opzioni a discesa
6. Selezionare il gruppo di risorse in cui si vuole spostare l'insieme di credenziali delle chiavi
7. Confermare l'avviso relativo allo scambio di risorse
8. Scegliere "OK"

Key Vault ora valuterà la validità dello spostamento di risorse e segnalerà eventuali errori. Se non vengono rilevati errori, lo spostamento delle risorse verrà completato. 
