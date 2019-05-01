---
title: Procedure consigliate per l'operatore - Identità nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per l'operatore del cluster per la gestione dell'autenticazione e dell'autorizzazione per i cluster nel servizio Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: iainfou
ms.openlocfilehash: 1c20e7796d152c9198786c491f9a61752d88ea6f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726620"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes (AKS)

Durante la distribuzione e la gestione dei cluster nel servizio Azure Kubernetes (AKS), è necessario implementare opportune modalità di gestione dell'accesso a risorse e servizi. Senza questi controlli, gli account possono avere accesso alle risorse e ai servizi di cui non hanno bisogno. Può essere inoltre difficile tenere traccia del set di credenziali usato per apportare le modifiche.

Questo articolo sulle procedure consigliate è incentrato su come un operatore del cluster può gestire l'accesso e l'identità per i cluster servizio Azure Kubernetes. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Autenticare gli utenti del cluster servizio Azure Kubernetes con Azure Active Directory
> * Controllare l'accesso alle risorse con i controlli degli accessi in base al ruolo
> * Usare un'identità gestita per autenticarsi con altri servizi

## <a name="use-azure-active-directory"></a>Usare Azure Active Directory

**Indicazioni sulle procedure consigliate**. Distribuire i cluster servizio Azure Kubernetes con l'integrazione di Azure AD. L'uso di Azure AD consente di centralizzare il componente di gestione delle identità. Qualsiasi modifica all'account utente o allo stato del gruppo viene aggiornata automaticamente nell'accesso al cluster servizio Azure Kubernetes. Usare ruoli o ClusterRole e associazioni, come descritto nella sezione successiva, per assegnare a utenti o gruppi il minor numero di autorizzazioni richieste.

Gli sviluppatori e i proprietari delle applicazioni del cluster Kubernetes hanno bisogno di accedere a risorse diverse. Kubernetes non offre una soluzione di gestione delle identità per controllare quali utenti possono interagire con determinate risorse. In alternativa, in genere si integra il cluster con una soluzione di gestione delle identità esistente. Azure Active Directory (AD) offre una soluzione di gestione delle identità di livello aziendale e può essere integrato con i cluster servizio Azure Kubernetes.

Con i cluster integrati con Azure AD in servizio Azure Kubernetes, vengono creati *ruoli* o *ClusterRole* che definiscono le autorizzazioni di accesso alle risorse. Si *associano* quindi i ruoli a utenti o gruppi da Azure AD. Questi controlli degli accessi in base al ruolo di Kubernetes sono descritti nella sezione successiva. L'integrazione di Azure AD e la modalità di controllo dell'accesso alle risorse possono essere visualizzate nel diagramma seguente:

![Autenticazione a livello di cluster per l'integrazione di Azure Active Directory con servizio Azure Kubernetes](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Lo sviluppatore viene autenticato con Azure AD.
1. L'endpoint di emissione del token di Azure AD emette il token di accesso.
1. Lo sviluppatore esegue un'azione usando il token di Azure AD, ad esempio `kubectl create pod`.
1. Kubernetes convalida il token con Azure Active Directory e recupera le appartenenze al gruppo dello sviluppatore.
1. Vengono applicati i criteri cluster e il controllo degli accessi in base al ruolo di Kubernetes.
1. La richiesta dello sviluppatore ha esito positivo o negativo a seconda della precedente convalida dell'appartenenza al gruppo di Azure AD e dei criteri e del controllo degli accessi in base al ruolo di Kubernetes.

Per creare un cluster AKS che usa Azure AD, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Usare i controlli degli accessi in base al ruolo

**Indicazioni sulle procedure consigliate**. Usare i controlli degli accessi in base al ruolo di Kubernetes per definire le autorizzazioni di utenti o gruppi relativamente alle risorse del cluster. Creare ruoli e associazioni che assegnano il numero minimo di autorizzazioni richieste. Consentire l'integrazione con Azure AD in modo che qualsiasi modifica allo stato dell'utente o all'appartenenza al gruppo venga automaticamente aggiornata e l'accesso alle risorse del cluster sia corrente.

In Kubernetes è possibile fornire un controllo granulare dell'accesso alle risorse del cluster. Le autorizzazioni possono essere definite a livello di cluster o per spazi dei nomi specifici. È possibile definire quali risorse possono essere gestite e con quali autorizzazioni. Questi ruoli vengono applicati a utenti o gruppi con un'associazione. Per altre informazioni su *ruoli*, *ClusterRole* e *associazioni*, vedere [Opzioni di accesso e identità per il servizio Azure Kubernetes][aks-concepts-identity].

Ad esempio, è possibile creare un ruolo che conceda l'accesso completo alle risorse dello spazio dei nomi denominato *finance-app*, come illustrato nell'esempio di manifesto YAML seguente:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Viene quindi creato che associa l'utente di Azure AD un RoleBinding *developer1\@contoso.com* a RoleBinding, come illustrato nel manifesto YAML seguente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1\@contoso.com* viene autenticato nel cluster AKS, dispongono delle autorizzazioni complete per le risorse nel *finance-app* dello spazio dei nomi. In questo modo, l'accesso alle risorse viene separato e controllato logicamente. Il controllo degli accessi in base al ruolo di Kubernetes deve essere usato in combinazione con l'integrazione di Azure AD, come descritto nella sezione precedente.

Per informazioni su come usare i gruppi di Azure AD per controllare l'accesso alle risorse di Kubernetes tramite RBAC, vedere [controllare l'accesso alle risorse del cluster con i controlli di accesso basato sui ruoli e le identità di Azure Active Directory nel servizio contenitore di AZURE] [ azure-ad-rbac].

## <a name="use-pod-identities"></a>Usare le identità del pod

**Indicazioni sulle procedure consigliate**. Non usare credenziali fisse all'interno dei pod o delle immagini del contenitore, essendo a rischio di esposizione o uso improprio. In alternativa, usare le identità del pod per richiedere automaticamente l'accesso tramite una soluzione centrale di gestione delle identità di Azure AD.

Quando i pod richiedono l'accesso ad altri servizi di Azure, ad esempio Cosmos DB, Key Vault o Archiviazione BLOB, il pod necessita delle credenziali di accesso. Queste credenziali di accesso possono essere definite con l'immagine del contenitore o inserite come un segreto Kubernetes, ma devono essere create e assegnate manualmente. Spesso, le credenziali sono riutilizzate tra i pod e non vengono ruotate regolarmente.

Identità gestita per le risorse di Azure (attualmente implementate come un progetto open source AKS associato) consentono di automaticamente richieste di accesso ai servizi tramite Azure AD. Le credenziali per i pod non vengono definite manualmente, ma è possibile richiedere un token di accesso in tempo reale da usare per accedere solo ai relativi servizi assegnati. In servizio Azure Kubernetes vengono distribuiti due componenti dall'operatore del cluster per consentire ai pod di usare le identità gestite:

* Il **server NMI (Node Management Identity)** è un pod che viene eseguito come DaemonSet su ogni nodo nel cluster servizio Azure Kubernetes. Il server NMI ascolta le richieste del pod ai servizi di Azure.
* Il **controller MIC (Managed Identity Controller)** è un pod centrale che dispone di autorizzazioni per eseguire query nel server API Kubernetes e verifica la presenza di un mapping delle identità di Azure corrispondente a un pod.

Quando i pod richiedono l'accesso a un servizio di Azure, le regole di rete reindirizzano il traffico verso il server NMI (Node Management Identity). Il server NMI identifica i pod che richiedono l'accesso ai servizi di Azure in base al relativo indirizzo remoto ed esegue una query nel controller MIC (Managed Identity Controller). Il MIC verifica la presenza dei mapping delle identità di Azure nel cluster servizio Azure Kubernetes e il server NMI richiede quindi un token di accesso da Azure Active Directory (AD) in base al mapping delle identità del pod. Azure AD fornisce l'accesso al server NMI, che viene restituito al pod. Questo token di accesso può essere quindi usato dal pod per richiedere l'accesso ai servizi di Azure.

Nell'esempio seguente uno sviluppatore crea un pod che usa un'identità gestita per richiedere l'accesso a un'istanza del server SQL Azure:

![Le identità del pod consentono a un pod di richiedere automaticamente l'accesso ad altri servizi](media/operator-best-practices-identity/pod-identities.png)

1. L'operatore del cluster crea innanzitutto un account del servizio che può essere usato per eseguire il mapping delle identità quando i pod richiedono l'accesso ai servizi.
1. Il server NMI e il MIC vengono distribuiti per inoltrare le richieste dei pod per i token di accesso ad Azure AD.
1. Uno sviluppatore distribuisce un pod con un'identità gestita che richiede un token di accesso tramite il server NMI.
1. Il token viene restituito al pod e usato per accedere a un'istanza del server SQL Azure.

> [!NOTE]
> Le identità di pod gestito è un progetto open source e non è supportata dal supporto tecnico di Azure.

Per usare le identità del pod, vedere l'articolo sulle [identità di Azure Active Directory per le applicazioni Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sulle procedure consigliate ha illustrato l'autenticazione e l'autorizzazione per il cluster e le risorse. Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Integrare Azure Active Directory con servizio Azure Kubernetes][aks-aad]
* [Usare identità gestite per le risorse di Azure con servizio Azure Kubernetes][aad-pod-identity]

Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-scheduler]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
