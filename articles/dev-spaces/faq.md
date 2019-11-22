---
title: Domande frequenti su Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Trovare le risposte ad alcune domande comuni su Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 3c7335f1656d304d231c2146c8b7496ea43f0b4c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280246"
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

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>È possibile usare Azure Dev Spaces nei cluster AKS con gli intervalli di indirizzi IP autorizzati del server API abilitati?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con gli [intervalli di indirizzi IP autorizzati del server API][aks-auth-range] abilitati. Quando si [Crea][aks-auth-range-create] il cluster, è necessario [consentire intervalli aggiuntivi in base all'area geografica][aks-auth-range-ranges]. È anche possibile [aggiornare][aks-auth-range-update] un cluster esistente per consentire tali intervalli aggiuntivi.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>È possibile usare Azure Dev Spaces nei cluster AKS con traffico in uscita limitato per i nodi del cluster?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con [traffico in uscita limitato per i nodi del cluster][aks-restrict-egress-traffic] abilitati una volta consentiti i nomi di dominio completi seguenti:

| FQDN                                    | Port      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Per eseguire il pull di immagini Linux alpine e di altro Azure Dev Spaces |
| gcr.io | HTTP: 443 | Per estrarre le immagini Helm/Tiller|
| storage.googleapis.com | HTTP: 443 | Per estrarre le immagini Helm/Tiller|


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md