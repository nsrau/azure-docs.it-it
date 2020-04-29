---
title: Azure Key Vault lo trasferimento di un insieme di credenziali in un'area diversa | Microsoft Docs
description: Linee guida per lo trasferimento di un insieme di credenziali delle chiavi in un'area diversa.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254148"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Trasferimento di un Azure Key Vault tra aree

## <a name="overview"></a>Panoramica

Key Vault non supporta un'operazione di spostamento delle risorse che consente lo spostamento di un insieme di credenziali delle chiavi in un'altra area. In questo articolo vengono illustrate le soluzioni alternative se si ha bisogno di un'azienda per spostare un insieme di credenziali delle chiavi in un'altra area. Ogni opzione presenta alcune limitazioni ed è fondamentale comprendere le implicazioni di queste soluzioni alternative prima di provare in un ambiente di produzione.

Se è necessario spostare un insieme di credenziali delle chiavi in un'altra area, la soluzione consiste nel creare un nuovo insieme di credenziali delle chiavi nell'area desiderata e copiare manualmente ogni singolo segreto dall'insieme di credenziali delle chiavi esistente al nuovo insieme di credenziali delle chiavi. Questa operazione può essere eseguita in uno dei modi seguenti elencati di seguito.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

* I nomi di Key Vault sono univoci a livello globale. Non sarà possibile riutilizzare lo stesso nome dell'insieme di credenziali.

* Sarà necessario riconfigurare i criteri di accesso e le impostazioni di configurazione di rete nel nuovo insieme di credenziali delle chiavi.

* Sarà necessario riconfigurare l'eliminazione temporanea e ripulire la protezione nel nuovo insieme di credenziali delle chiavi.

* L'operazione di backup e ripristino non consentirà di mantenere le impostazioni di rotazione automatica, potrebbe essere necessario riconfigurare tali impostazioni.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Opzione 1: usare i comandi di backup e ripristino dell'insieme di credenziali delle chiavi

È possibile eseguire il backup di ogni singolo segreto, chiave e certificato nell'insieme di credenziali usando il comando backup. I segreti verranno scaricati come BLOB crittografati. È quindi possibile ripristinare il BLOB nel nuovo insieme di credenziali delle chiavi. I comandi sono documentati nel collegamento seguente.

[Comandi Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Limitazioni

* Non è possibile eseguire il backup di un insieme di credenziali delle chiavi in una geografia e ripristinarlo in un'altra geografia. Scopri di più sulle aree geografiche di Azure. [Collegamento](https://azure.microsoft.com/global-infrastructure/geographies/)

* Il comando backup esegue il backup di tutte le versioni di ogni segreto. Se si dispone di un segreto con un numero elevato di versioni precedenti (maggiore di 10), è possibile che la richiesta superi le dimensioni massime consentite della richiesta e che l'operazione abbia esito negativo.

## <a name="option-2---manually-download-and-upload-secrets"></a>Opzione 2: scaricare e caricare manualmente i segreti

Alcuni tipi di segreto possono essere scaricati manualmente. Ad esempio, è possibile scaricare i certificati come file con estensione pfx. Questa opzione Elimina le restrizioni geografiche per alcuni tipi di segreto, ad esempio i certificati. È possibile caricare i file con estensione pfx in qualsiasi insieme di credenziali delle chiavi in qualsiasi area. Il segreto verrà scaricato in un formato non protetto da password. Si sarà responsabili della protezione dei segreti una volta lasciati Key Vault durante l'esecuzione dello spostamento.
