---
title: Creare il nome di dominio completo per una macchina virtuale nel portale di Azure
description: Informazioni su come creare un nome di dominio completo (FQDN) per una macchina virtuale nell'portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351910"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Linux

Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com), il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN. 

## <a name="create-a-fqdn"></a>Creare un nome di dominio completo
In questo articolo si presuppone che sia già stata creata una VM. Se necessario, è possibile creare una VM [Linux](./linux/quick-create-portal.md) o [Windows](./windows/quick-create-portal.md) nel portale. Dopo che la macchina virtuale è operativa, seguire questi passaggi:


1. Selezionare la macchina virtuale nel portale. In **nome DNS** selezionare **Configura**.
2. Immettere il nome DNS e quindi selezionare **Save (Salva** ) nella parte superiore della pagina.
3. Per tornare al pannello panoramica della macchina virtuale, chiudere il pannello **configurazione** selezionando la **X** nell'angolo superiore destro. 
4. Verificare che il *nome DNS* sia ora visualizzato correttamente.
   



## <a name="next-steps"></a>Passaggi successivi
Ora che la macchina virtuale ha un indirizzo IP pubblico e un nome DNS, è possibile distribuire Framework applicazioni comuni o servizi come Nginx, MongoDB e docker.

Per suggerimenti sulla creazione di distribuzioni di Azure, vedere la pagina relativa all'[uso di Azure Resource Manager](../azure-resource-manager/management/overview.md).

