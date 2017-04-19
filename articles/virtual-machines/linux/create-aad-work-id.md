---
title: "Creare un&quot;identità aziendale o dell&quot;istituto di istruzione in AAD per Linux | Documentazione Microsoft"
description: "Informazioni su come creare un&quot;identità di lavoro o scuola in Azure Active Directory da usare con le macchine virtuali Linux."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: b0f86d77-c669-4aa1-a095-c2aa4d9105fe
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4fdb72e3eec41d66f8561baf1755aa425ac278af
ms.lasthandoff: 04/03/2017


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Creazione di un'identità di lavoro o scuola in Azure Active Directory da usare con macchine virtuali Linux
Se si è creato un account Azure personale, oppure si dispone di una sottoscrizione MSDN personale e si è creato l'account Azure per sfruttare i vantaggi dei crediti MSDN in Azure, si è usata un'identità *account Microsoft* . Numerose funzionalità di Azure, ad esempio i [modelli dei gruppi di risorse](../../azure-resource-manager/resource-group-overview.md), per funzionare richiedono un account aziendale o dell'istituto di istruzione (un'identità gestita da Azure Active Directory). Per creare un nuovo account aziendale o dell’istituto d’istruzione è possibile seguire le seguenti istruzioni poichè fortunatamente, uno degli aspetti migliori dell'account Azure personale sta nel fatto che è dotato di un dominio predefinito di Azure Active Directory, che è possibile usare per creare un nuovo account aziendale o dell’istituto d’istruzione da usare con le funzionalità di Azure che lo richiedono.

Tuttavia, le modifiche più recenti consentono di gestire la sottoscrizione con qualsiasi tipo di account Azure tramite il metodo di accesso interattivo `azure login` descritto [qui](../../xplat-cli-connect.md). È possibile utilizzare tale meccanismo oppure seguire le istruzioni che seguono. È anche possibile [creare un'identità di lavoro o scuola in Azure Active Directory da usare con macchine virtuali Windows](../windows/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../../includes/virtual-machines-common-create-aad-work-id.md)]


