---
title: Domande frequenti su Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Trovare le risposte ad alcune domande comuni su Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305977"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Domande frequenti su Azure Dev Spaces

Questo indirizzo viene indirizzato a domande frequenti su Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quali aree di Azure forniscono attualmente Azure Dev Spaces?

Per un elenco completo delle aree disponibili, vedere [le aree e le configurazioni supportate][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>È possibile usare Azure Dev Spaces senza un indirizzo IP pubblico?

No, non è possibile effettuare il provisioning di Azure Dev Spaces in un cluster AKS senza un indirizzo IP pubblico. Un indirizzo IP pubblico è [necessario per Azure Dev Spaces per il routing][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>È possibile usare l'ingresso personale con Azure Dev Spaces?

Sì, è possibile configurare il proprio ingresso lungo il lato di un Azure Dev Spaces creato. Ad esempio, è possibile usare [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>È possibile utilizzare HTTPS con Azure Dev Spaces?

Sì, è possibile configurare il traffico in ingresso con HTTPS usando [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>È possibile usare Azure Dev Spaces in un cluster che usa CNI anziché kubenet? 

Sì, è possibile usare Azure Dev Spaces in un cluster AKS che usa CNI per la rete. Ad esempio, è possibile usare Azure Dev Spaces in un cluster AKS con i [contenitori di Windows esistenti][windows-containers], che usa CNI per la rete.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>È possibile usare Azure Dev Spaces con I contenitori di Windows?

Attualmente, Azure Dev Spaces è progettato per l'esecuzione solo su Pod e nodi Linux, ma è possibile eseguire Azure Dev Spaces in un cluster AKS con i [contenitori di Windows esistenti][windows-containers].


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md