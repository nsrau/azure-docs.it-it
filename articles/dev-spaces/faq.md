---
title: Domande frequenti su Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Trovare le risposte ad alcune domande comuni su Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 739cfdb4f930d384e180ffd0f3ce2311cd41c70a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207995"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Domande frequenti su Azure Dev Spaces

Questo indirizzo viene indirizzato a domande frequenti su Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Quali versioni di Kubernetes sono supportate per Azure Dev Spaces?

Azure Dev Spaces supporta tutte le [versioni di disponibilità generale (GA) attualmente supportate di Kubernetes in AKS][aks-supported-k8s].

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quali aree di Azure forniscono attualmente Azure Dev Spaces?

Per un elenco completo delle aree disponibili, vedere [aree supportate][supported-regions] .

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>È possibile eseguire la migrazione del cluster AKS con Azure Dev Spaces in un'altra area?

Sì. Se si vuole spostare il cluster AKS con Azure Dev Spaces in un'altra [area supportata][supported-regions], è consigliabile creare un nuovo cluster nell'altra area, quindi installare e configurare Azure Dev Spaces e distribuire le risorse e le applicazioni nel nuovo cluster. Per altre informazioni sulla migrazione di AKS, vedere [eseguire la migrazione al servizio Azure Kubernetes (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usare Azure Dev Spaces con I grafici Dockerfile o Helm esistenti?

Sì, se il progetto dispone già di un Dockerfile o di un grafico Helm, è possibile usare tali file con Azure Dev Spaces. Quando si esegue `azds prep` , utilizzare il `--chart` parametro e specificare il percorso del grafico. Azure Dev Spaces genererà comunque un file *azds. YAML* e *Dockerfile. develop* , ma non sostituirà né modificherà un Dockerfile o un grafico Helm esistente. Potrebbe essere necessario modificare i file *azds. YAML* e *Dockerfile. develop* in modo che tutto funzioni correttamente con l'applicazione esistente durante l'esecuzione di `azds up` .

Quando si usa il proprio Dockerfile o il proprio grafico Helm, esistono le limitazioni seguenti:
* Se si usa un solo Dockerfile, deve includere tutto il necessario per abilitare gli scenari di sviluppo, ad esempio il linguaggio SDK non solo il Runtime. Se si usa un Dockerfile separato per Azure Dev Spaces, ad esempio Dockerfile. develop, tutti gli elementi necessari per abilitare gli scenari di sviluppo devono essere inclusi in tale Dockerfile.
* Il grafico Helm deve supportare il passaggio di una parte o l'intero tag immagine come valore da *values. YAML*.
* Se si modificano le operazioni in ingresso, è anche possibile aggiornare il grafico Helm per usare la soluzione di ingresso fornita da Azure Dev Spaces.
* Se si desidera utilizzare le [funzionalità di routing fornite da Azure Dev Spaces][dev-spaces-routing], tutti i servizi per un singolo progetto devono rientrare in un singolo spazio dei nomi Kubernetes e devono essere distribuiti con una denominazione semplice, ad esempio *Service-a*. Nei grafici Helm standard, questo aggiornamento dei nomi può essere eseguito specificando un valore per la proprietà *fullnameOverride* .

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>È possibile modificare i file generati da Azure Dev Spaces?

Sì, è possibile modificare il file *azds. YAML* , Dockerfile e il grafico Helm [generato da Azure Dev Spaces durante la preparazione del progetto][dev-spaces-prep]. La modifica di questi file cambia in che modo il progetto viene compilato ed eseguito.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>È possibile usare Azure Dev Spaces senza un indirizzo IP pubblico?

No, non è possibile effettuare il provisioning di Azure Dev Spaces in un cluster AKS senza un indirizzo IP pubblico. Un indirizzo IP pubblico è [necessario per Azure Dev Spaces per il routing][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>È possibile usare l'ingresso personale con Azure Dev Spaces?

Sì, è possibile configurare il proprio ingresso lungo il lato in ingresso Azure Dev Spaces crea. Ad esempio, è possibile usare [traefik][ingress-traefik] o [nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>È possibile utilizzare HTTPS con Azure Dev Spaces?

Sì, è possibile configurare il traffico in ingresso con HTTPS usando [traefik][ingress-https-traefik] o [nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>È possibile usare Azure Dev Spaces in un cluster che usa CNI anziché kubenet? 

Sì, è possibile usare Azure Dev Spaces in un cluster AKS che usa CNI per la rete. Ad esempio, è possibile usare Azure Dev Spaces in un cluster AKS con i [contenitori di Windows esistenti][windows-containers], che usa CNI per la rete. Altre informazioni sull'uso di CNI per la rete con Azure Dev Spaces sono disponibili [qui](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>È possibile usare Azure Dev Spaces con I contenitori di Windows?

Attualmente, Azure Dev Spaces è progettato per l'esecuzione solo su Pod e nodi Linux, ma è possibile eseguire Azure Dev Spaces in un cluster AKS con i [contenitori di Windows esistenti][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>È possibile usare Azure Dev Spaces nei cluster AKS con gli intervalli di indirizzi IP autorizzati del server API abilitati?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con gli [intervalli di indirizzi IP autorizzati del server API][aks-auth-range] abilitati. Altre informazioni sull'uso di un cluster AKS con gli intervalli di indirizzi IP autorizzati del server API abilitati con Azure Dev Spaces sono disponibili [qui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>È possibile usare Azure Dev Spaces nei cluster AKS con traffico in uscita limitato per i nodi del cluster?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con [traffico in uscita limitato per i nodi del cluster][aks-restrict-egress-traffic] abilitati dopo che sono stati consentiti i nomi di dominio completi corretti. Altre informazioni sull'uso di un cluster AKS con traffico in uscita limitato per i nodi del cluster abilitato con Azure Dev Spaces sono disponibili [qui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>È possibile usare Azure Dev Spaces nei cluster AKS abilitati per RBAC?

Sì, è possibile usare Azure Dev Spaces nei cluster AKS con o senza RBAC abilitato.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Cosa accade quando si Abilita l'ingresso per il progetto in Visual Studio?

Quando si usa Visual Studio per preparare il progetto, è possibile abilitare il traffico in ingresso per il servizio. L'abilitazione di ingresso crea un endpoint pubblico per accedere al servizio durante l'esecuzione nel cluster AKS, che è facoltativo. Se non si Abilita l'ingresso, il servizio è accessibile solo dall'interno del cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>È possibile usare le identità gestite di Pod con Azure Dev Spaces?

Sì, è possibile usare le [identità gestite di Pod][aks-pod-managed-id] nei cluster AKS con Azure Dev Spaces abilitata, ma dopo aver abilitato Azure Dev Spaces nel cluster con le identità gestite da Pod, è necessario eseguire [passaggi di configurazione aggiuntivi][dev-spaces-pod-managed-id-steps] . Se sono installate identità gestite da Pod e si vuole disinstallarla, è possibile trovare altre informazioni nelle note di [disinstallazione][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>È possibile usare Azure Dev Spaces con più microservizi in un'applicazione?

Sì, è possibile usare Azure Dev Spaces in un'applicazione con più microservizi, ma è necessario preparare ed eseguire i singoli microservizi alla radice. L'interfaccia della riga di comando di Azure Dev Spaces, Azure Dev Spaces VS Code estensione e il carico di lavoro di sviluppo di Visual Studio Azure prevede che il file *azds. YAML* sia alla radice del microservizio per poter eseguire il debug. Vedere l' [applicazione di esempio bike sharing][bike-sharing] per un esempio di più microservizi in un'unica applicazione.

In Visual Studio Code, è possibile [aprire progetti distinti in una singola area di lavoro][vs-code-multi-root-workspaces] ed eseguirne il debug separatamente tramite Azure Dev Spaces. Ognuno dei progetti deve essere indipendente e preparato per Azure Dev Spaces.

In Visual Studio è possibile configurare soluzioni .NET Core per eseguire il debug tramite Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>È possibile usare Azure Dev Spaces con una rete mesh di servizi?

Al momento non è possibile usare Azure Dev Spaces con mesh di servizi, ad esempio [Istio][istio] o [Linkerd][linkerd]. È possibile eseguire Azure Dev Spaces e una mesh di servizi nello stesso cluster AKS, ma non è possibile avere sia Azure Dev Spaces che una mesh di servizi abilitati nello stesso spazio dei nomi.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md