---
title: Utilizzo di Terraform con Azure
description: Introduzione all'uso di Terraform per il controllo delle versioni e la distribuzione dell'infrastruttura di Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472163"
---
# <a name="terraform-with-azure"></a>Utilizzo di Terraform con Azure

[Hashicorp Terraform](https://www.terraform.io/) è uno strumento open source per il provisioning e la gestione dell'infrastruttura cloud. Codifica l'infrastruttura nei file di configurazione che descrivono la topologia delle risorse cloud. Queste risorse includono macchine virtuali, account di archiviazione e interfacce di rete. L'interfaccia della riga di comando di Terraform offre un meccanismo semplice per la distribuzione e il controllo della versione dei file di configurazione in Azure.

Questo articolo descrive i vantaggi dell'utilizzo di Terraform nella gestione dell'infrastruttura di Azure.

## <a name="automate-infrastructure-management"></a>Automatizzare la gestione dell'infrastruttura.

I file di configurazione basati su modello di Terraform consentono di definire, eseguire il provisioning e configurare le risorse di Azure in modo prevedibile e ripetibile. L'automazione dell'infrastruttura ha diversi vantaggi:

- Riduce il rischio di errori umani durante la distribuzione e la gestione dell'infrastruttura.
- Distribuisce lo stesso modello più volte per creare ambienti di sviluppo, test e produzione identici.
- Riduce i costi degli ambienti di sviluppo e test creandoli su richiesta.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Comprendere le modifiche apportate all'infrastruttura prima che vengano applicate

Quando una topologia di risorsa diventa complessa, comprendere il significato e l'impatto delle modifiche apportate all'infrastruttura può essere difficile.

L'interfaccia della riga di comando di Terraform consente agli utenti di convalidare e visualizzare in anteprima le modifiche apportate all'infrastruttura prima dell'applicazione. La visualizzazione in anteprima delle modifiche all'infrastruttura in modo sicuro offre numerosi vantaggi:
- I membri del team possono collaborare efficacemente conoscendo le modifiche proposte e il relativo impatto.
- È possibile rilevare le modifiche non intenzionali nelle prime fasi del processo di sviluppo.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuire l'infrastruttura su più cloud

Terraform consente di distribuire un'infrastruttura tra più provider di servizi cloud. Permette inoltre agli sviluppatori di usare strumenti coerenti per gestire ogni definizione di infrastruttura.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver letto la panoramica su Terraform e i relativi vantaggi, ecco i passaggi successivi suggeriti:

- Per iniziare, [installare Terraform e configurarlo per l'utilizzo in Azure](terraform-install-configure.md).
- [Creare una macchina virtuale di Azure usando Terraform](terraform-create-complete-vm.md)
- Esplorare il [modulo di Azure Resource Manager per Terraform](https://www.terraform.io/docs/providers/azurerm/). 