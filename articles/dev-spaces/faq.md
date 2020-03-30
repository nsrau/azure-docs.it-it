---
title: Domande frequenti su Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Risposte ad alcune delle domande comuni su Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: e7b4620faa01aa9f6d46c34bafb1c623c338beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240507"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Domande frequenti su Azure Dev Spaces

Vengono fornite informazioni frequenti sugli spazi di sviluppo di Azure.This addresses frequently asked questions about Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quali aree di Azure forniscono attualmente Azure Dev Spaces?

Per un elenco completo delle aree disponibili, vedere [Aree geografiche supportate.][supported-regions]

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>È possibile eseguire la migrazione del cluster AKS con Azure Dev Spaces in un'altra area?

Sì, se si desidera spostare il cluster AKS con Azure Dev Spaces in un'altra [area supportata,][supported-regions]è consigliabile creare un nuovo cluster nell'altra area, installare e configurare Azure Dev Spaces e distribuire le risorse e le applicazioni nel nuovo cluster. Per altre informazioni sulla migrazione di AKS, vedere [Eseguire la migrazione al servizio Azure Kubernetes (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>È possibile usare Azure Dev Spaces con i file Docker o Helm esistenti?

Sì, se il progetto include già un Dockerfile o un grafico Helm, è possibile usare tali file con Azure Dev Spaces.Yes, if your project already has a Dockerfile or a Helm chart, you can use those files with Azure Dev Spaces. Quando si `azds prep`esegue `--chart` , utilizzare il parametro e specificare la posizione del grafico. Azure Dev Spaces genererà comunque un file *azds.yaml* e *Dockerfile.develop,* ma non sostituirà o modificherà un dockerfile esistente o un grafico Helm. Potrebbe essere necessario modificare i file *azds.yaml* e *Dockerfile.develop* affinché tutti `azds up`funzionino correttamente con l'applicazione esistente durante l'esecuzione di .

Quando si utilizza il proprio dockerfile o Helm grafico, esistono le seguenti limitazioni:
* Se si usa un solo Dockerfile, deve includere tutto il necessario per abilitare gli scenari di sviluppo, ad esempio l'SDK del linguaggio, non solo il runtime. Se si usa un Dockerfile separato per Azure Dev Spaces, ad esempio Dockerfile.develop, tutto ciò che è necessario per abilitare gli scenari di sviluppo deve essere incluso in tale Dockerfile.If using a separate Dockerfile for Azure Dev Spaces, such as a Dockerfile.develop, everything you need to enable development scenarios must be included in that Dockerfile.
* Il grafico Helm deve supportare il passaggio di parte o dell'intero tag immagine come valore da *values.yaml*.
* Se si modifica qualsiasi elemento con ingresso in ingresso, è anche possibile aggiornare il grafico Helm per usare la soluzione in ingresso fornita da Azure Dev Spaces.If you are modifying anything with ingress, you can also update your Helm chart to use the ingress solution provided by Azure Dev Spaces.
* Se si desidera utilizzare le funzionalità di [routing fornite da Azure Dev Spaces][dev-spaces-routing], tutti i servizi per un singolo progetto devono rientrare in un singolo spazio dei nomi Kubernetes e devono essere distribuiti con una denominazione semplice, ad esempio *service-a*. Nei grafici Helm standard, questo aggiornamento dei nomi può essere eseguito specificando un valore per la proprietà *fullnameOverride.*

Per confrontare il proprio dockerfile o Helm chart con una versione esistente che funziona con Azure Dev Spaces, esaminare i file generati nella [guida introduttiva][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>È possibile modificare i file generati da Azure Dev Spaces?

Sì, è possibile modificare il file *azds.yaml,* Dockerfile e Helm [generati da Azure Dev Spaces durante la preparazione del progetto.][dev-spaces-prep] La modifica di questi file modifica la modalità di compilazione ed esecuzione del progetto.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>È possibile usare Azure Dev Spaces senza un indirizzo IP pubblico?

No, non è possibile eseguire il provisioning di Azure Dev Spaces in un cluster AKS senza un indirizzo IP pubblico. Un indirizzo IP pubblico è necessario per gli spazi di sviluppo di [Azure per il routing.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>È possibile usare il proprio ingresso con Azure Dev Spaces?

Sì, è possibile configurare il proprio ingresso lungo il lato creato da Azure Dev Spaces in ingresso. Ad esempio, è possibile utilizzare [traefik][ingress-traefik] o [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>È possibile usare HTTPS con Azure Dev Spaces?

Sì, è possibile configurare il proprio ingresso con HTTPS utilizzando [traefik][ingress-https-traefik] o [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>È possibile usare Spazi di sviluppo di Azure in un cluster che usa CNI anziché kubenet? 

Sì, è possibile usare Azure Dev Spaces in un cluster AKS che usa CNI per la rete. Ad esempio, è possibile usare Azure Dev Spaces in un cluster AKS con [contenitori di Windows esistenti,][windows-containers]che usa CNI per la rete. Ulteriori informazioni sull'uso di CNI per la connessione in rete con Azure Dev Spaces sono disponibili [qui.](configure-networking.md#using-azure-cni)

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>È possibile usare Azure Dev Spaces con i contenitori di Windows?

Attualmente, Azure Dev Spaces è destinato a essere eseguito solo su pod e nodi Linux, ma è possibile eseguire Azure Dev Spaces in un cluster AKS con [contenitori Windows esistenti.][windows-containers]

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>È possibile usare Azure Dev Spaces nei cluster AKS con gli intervalli di indirizzi IP autorizzati al server API abilitati?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con [gli intervalli][aks-auth-range] di indirizzi IP autorizzati al server API abilitati. Altre informazioni sull'uso di un cluster AKS con intervalli di indirizzi IP autorizzati al server API abilitati con Azure Dev Spaces sono disponibili [qui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>È possibile usare Spazi di sviluppo di Azure nei cluster AKS con traffico in uscita limitato per i nodi del cluster?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con [traffico in uscita limitato per i nodi del cluster][aks-restrict-egress-traffic] abilitato dopo aver consentito il numero di FQDN corretto. Altre informazioni sull'uso di un cluster AKS con traffico in uscita limitato per i nodi del cluster abilitato con Azure Dev Spaces sono disponibili [qui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>È possibile usare Azure Dev Spaces nei cluster AKS abilitati al controllo degli accessi in base al ruolo?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con o senza controllo degli accessi in base al ruolo abilitato.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Cosa succede quando si abilita l'ingresso per il progetto in Visual Studio?

Quando si usa Visual Studio per preparare il progetto, è possibile abilitare l'ingresso per il servizio. L'abilitazione dell'ingresso crea un endpoint pubblico per accedere al servizio quando viene eseguito nel cluster AKS, che è facoltativo. Se non si abilita l'ingresso, il servizio sarà accessibile solo dall'interno del cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>È possibile usare le identità gestite con pod con Azure Dev Spaces?

Attualmente, Azure Dev Spaces non supporta l'uso di [identità gestite di pod][aks-pod-managed-id] nei cluster AKS con Azure Dev Spaces abilitato. Se sono state installate identità gestite da pod e si desidera disinstallarle, è possibile trovare ulteriori dettagli nelle note di [disinstallazione.][aks-pod-managed-id-uninstall]

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md