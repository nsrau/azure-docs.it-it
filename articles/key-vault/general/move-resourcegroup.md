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
ms.openlocfilehash: ea152e1d78ab1ea610eee5420394c89524673993
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042383"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Trasferimento di una Azure Key Vault tra gruppi di risorse

## <a name="overview"></a>Panoramica

Lo stato di un insieme di credenziali delle chiavi tra gruppi di risorse è una funzionalità di Key Vault supportata. Lo stato di un insieme di credenziali delle chiavi tra gruppi di risorse non influirà sul firewall di Key Vault o sulle configurazioni dei criteri di accesso Le applicazioni connesse e le entità servizio devono continuare a funzionare come previsto.

> [!IMPORTANT]
> **Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati.**
> Se si usa Key Vault con la crittografia del disco per una macchina virtuale, l'insieme di credenziali delle chiavi non può essere spostato in un gruppo di risorse diverso o in una sottoscrizione mentre è abilitata la crittografia del disco. Prima di trasferire l'insieme di credenziali delle chiavi in un nuovo gruppo di risorse o una nuova sottoscrizione, è necessario disabilitare la crittografia del disco. 

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
