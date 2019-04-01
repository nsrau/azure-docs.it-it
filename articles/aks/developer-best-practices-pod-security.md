---
title: Procedure consigliate per gli sviluppatori - Sicurezza dei pod nel servizio Azure Kubernetes
description: Procedure consigliate per gli sviluppatori relative alla protezione dei pod nel servizio Azure Kubernetes
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1c2c5cbee91ddaee5f1f6af8ec17c48326f68e84
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755063"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza dei pod nel servizio Azure Kubernetes

La sicurezza dei pod è un fattore fondamentale da tenere in considerazione nello sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes. Le applicazioni dovrebbero essere progettate in modo da concedere il numero minimo di privilegi necessari. La protezione dei dati privati è la principale preoccupazione dei clienti. Nessuno vuole che credenziali come stringhe di connessione di database, chiavi, segreti o certificati vengano esposte al mondo esterno, dove un utente malintenzionato potrebbe sfruttarle per scopi dannosi. Evitare quindi di aggiungere questi tipi di dati al codice o di incorporarli nelle immagini del contenitore. In caso contrario si creerebbe un rischio di esposizione e si limiterebbe la possibilità di ruotare queste credenziali quando sarà necessario ricompilare le immagini del contenitore.

Questo articolo di procedure consigliate è incentrato sulla protezione dei pod nel servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Usare il contesto di protezione dei pod per limitare l'accesso a processi e servizi o l'escalation dei privilegi
> * Eseguire l'autenticazione con altre risorse di Azure usando le identità gestite dei pod
> * Richiedere e recuperare credenziali da un insieme di credenziali digitali come Azure Key Vault

È anche possibile leggere le procedure consigliate per la [sicurezza dei cluster][best-practices-cluster-security] e la [gestione delle immagini del contenitore][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Proteggere l'accesso dei pod alle risorse

**Indicazioni sulle procedure consigliate** - Per eseguire l'accesso con un account utente o di gruppo diverso e limitare l'accesso ai processi e servizi dei nodi sottostanti, definire le impostazioni del contesto di protezione dei pod. Assegnare il numero minimo di privilegi necessari.

Per la corretta esecuzione delle applicazioni, i pod dovrebbero essere eseguiti come utente o gruppo definito e non come *radice*. Il `securityContext` di un pod o un contenitore consente di definire impostazioni come *runAsUser* o *fsGroup* per assumere le autorizzazioni appropriate. Assegnare solo le autorizzazioni utente o di gruppo necessarie e non usare il contesto di protezione come mezzo per assumere autorizzazioni aggiuntive. In un contesto di esecuzione come utente non ROOT, i contenitori non possono eseguire il binding a porte con privilegi inferiori a 1024. In questo scenario è possibile usare i servizi Kubernetes per mascherare il fatto che un'app venga eseguita su una determinata porta.

Un contesto di protezione dei pod può anche definire ulteriori funzionalità o autorizzazioni per l'accesso a processi e servizi. È possibile impostare le seguenti definizioni comuni di contesto di protezione:

* **allowPrivilegeEscalation** definisce se il pod può assumere privilegi *radice*. Progettare le applicazioni in modo che questa impostazione sia sempre *false*.
* Le **funzionalità Linux** consentono al pod di accedere ai processi dei nodi sottostanti. Prestare attenzione nell'assegnazione di queste funzionalità. Assegnare il numero minimo di privilegi necessari. Per altre informazioni, vedere [Linux capabilities][linux-capabilities] (Funzionalità di Linux).
* Le **etichette SELinux** sono un modulo di sicurezza del kernel di Linux che consente di definire i criteri di accesso a servizi e processi e l'accesso al file system. Anche in questo caso, assegnare il numero minimo di privilegi necessari. Per altre informazioni, vedere [SELinuxOptions][selinux-labels] nella documentazione di Kubernetes.

Il seguente manifesto YAML di pod di esempio imposta le impostazioni del contesto di protezione da definire:

* Il pod viene eseguito come ID utente *1000* e parte dell'ID di gruppo *2000*
* Non è possibile eseguire l'escalation dei privilegi per usare `root`
* Le funzionalità di Linux possono accedere alle interfacce di rete e all'orologio in tempo reale (hardware) dell'host

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Consultare l'operatore del cluster per determinare le impostazioni del contesto di protezione necessarie. Cercare di progettare le applicazioni in modo da ridurre al minimo le autorizzazioni aggiuntive e l'accesso necessari al pod. Sono disponibili altre funzionalità di sicurezza per limitare l'accesso mediante AppArmor e seccomp (elaborazione sicura), che possono essere implementate dagli operatori cluster. Per altre informazioni, vedere [Proteggere l'accesso del contenitore alle risorse][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Limitare l'esposizione delle credenziali

**Indicazioni sulle procedure consigliate** - Non definire credenziali nel codice dell'applicazione. Usare le identità gestite per le risorse di Azure per consentire al pod di richiedere l'accesso ad altre risorse. Occorre anche usare un insieme di credenziali digitali, come Azure Key Vault, per archiviare e recuperare le chiavi e le credenziali digitali.

Per limitare il rischio di esposizione delle credenziali nel codice dell'applicazione, evitare l'uso di credenziali predefinite o condivise. Le credenziali o le chiavi non devono essere incluse direttamente nel codice. In caso di esposizione delle credenziali, è necessario aggiornare o ridistribuire l'applicazione. Un approccio migliore consiste nell'assegnare ai pod la propria identità e un modo per autenticare se stessi oppure nel recuperare automaticamente le credenziali da un insieme di credenziali digitali.

Quanto segue [AKS associato progetti open source] [ aks-associated-projects] consentono di autenticare automaticamente i POD o richiesta delle credenziali e chiavi da un insieme di credenziali digitale:

* Identità gestite per le risorse di Azure e
* Driver FlexVol di Azure Key Vault

Progetti open source AKS associati non sono supportati dal supporto tecnico di Azure. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. Questi progetti non sono consigliati per la produzione.

### <a name="use-pod-managed-identities"></a>Usare le identità gestite dei pod

Un'identità gestita per le risorse di Azure consente a un pod di autenticarsi con qualsiasi servizio di Azure che lo supporta, come Archiviazione o SQL. Al pod viene assegnata un'identità di Azure che gli consente di autenticarsi in Azure Active Directory e ricevere un token digitale. Questo token digitale può essere presentato ad altri servizi di Azure, che verificano se il pod è autorizzato ad accedere al servizio e ad eseguire le azioni necessarie. Con questo approccio non sono necessari segreti per le stringhe di connessione di database, ad esempio. Il flusso di lavoro semplificato per l'identità gestita del pod è illustrato nel diagramma seguente:

![Flusso di lavoro semplificato per l'identità gestita del pod in Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Un'identità gestita evita che il codice dell'applicazione debba includere le credenziali per accedere a un servizio, ad esempio Archiviazione di Azure. Poiché ogni pod esegue l'autenticazione con la propria identità, è possibile controllare e verificare l'accesso. Se l'applicazione si connette ad altri servizi di Azure, usare le identità gestite per limitare il riutilizzo delle credenziali e il rischio di esposizione.

Per altre informazioni sulle identità di pod, vedere [configurare un cluster servizio contenitore di AZURE per usare le identità di pod gestito e con le applicazioni][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Usare Azure Key Vault con FlexVol

Le identità gestite dei pod sono ideali per l'autenticazione con i servizi di Azure che supportano i pod. Per i propri servizi o applicazioni senza identità gestite per le risorse di Azure l'autenticazione viene ancora eseguita con credenziali o chiavi. Per archiviare queste credenziali è possibile usare un insieme di credenziali digitali.

Quando le applicazioni hanno bisogno di credenziali, comunicano con l'insieme di credenziali digitali, recuperano le credenziali più recenti e quindi si connettono al servizio necessario. Questo insieme di credenziali digitali può essere Azure Key Vault. Il flusso di lavoro semplificato per il recupero di credenziali da Azure Key Vault mediante le identità gestite del pod è illustrato nel diagramma seguente:

![Flusso di lavoro semplificato per il recupero di credenziali da Azure Key Vault mediante un'identità gestita del pod](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Con Azure Key Vault segreti come credenziali, chiavi dell'account di archiviazione o certificati vengono archiviati e sottoposti a regolare rotazione. È possibile integrare Azure Key Vault con un cluster AKS usando un driver FlexVolume. Questo driver consente al cluster AKS di recuperare le credenziali in modo nativo da Azure Key Vault e fornirle in tutta sicurezza solo al pod che le ha richieste. Consultare l'operatore del cluster per distribuire il driver FlexVol di Azure Key Vault nei nodi del servizio Azure Kubernetes. È possibile usare un'identità gestita del pod per richiedere l'accesso ad Azure Key Vault e recuperare le credenziali necessarie tramite il driver FlexVolume.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere i pod. Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Usare identità gestite per le risorse di Azure con servizio Azure Kubernetes][aad-pod-identity]
* [Integrare Azure Key Vault con il servizio Azure Kubernetes][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
