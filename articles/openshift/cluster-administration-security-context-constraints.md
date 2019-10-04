---
title: Gestire i vincoli del contesto di sicurezza in Azure Red Hat OpenShift | Microsoft Docs
description: Vincoli del contesto di sicurezza per gli amministratori del cluster OpenShift di Azure Red Hat
services: container-service
author: troy0820
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: afbde512ecb5a38eac38d6f5db614d92cd44c908
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937425"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Gestire i vincoli del contesto di sicurezza in Azure Red Hat OpenShift 

I vincoli del contesto di sicurezza (SCCs) consentono agli amministratori del cluster di controllare le autorizzazioni per i pod. Per ulteriori informazioni su questo tipo di API, vedere la [documentazione dell'architettura per SCCS](https://https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). È possibile gestire SCCs nell'istanza come oggetti API normali usando l'interfaccia della riga di comando.

## <a name="list-security-context-constraints"></a>Elencare i vincoli del contesto di sicurezza

Per ottenere un elenco corrente di SCCs, usare questo comando: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Esaminare un oggetto per i vincoli del contesto di sicurezza

Per esaminare un particolare SCC, utilizzare `oc get`, `oc describe` o `oc edit`.  Per esaminare, ad esempio, il cluster SCC **limitato** , utilizzare questo comando:
```bash
$ oc describe scc restricted
Name:                   restricted
Priority:               <none>
Access:
  Users:                <none>
  Groups:               system:authenticated
Settings:
  Allow Privileged:         false
  Default Add Capabilities:     <none>
  Required Drop Capabilities:       KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:         <none>
  Allowed Seccomp Profiles:     <none>
  Allowed Volume Types:         configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:           false
  Allow Host Ports:         false
  Allow Host PID:           false
  Allow Host IPC:           false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:          <none>
    UID Range Max:          <none>
  SELinux Context Strategy: MustRunAs
    User:               <none>
    Role:               <none>
    Type:               <none>
    Level:              <none>
  FSGroup Strategy: MustRunAs
    Ranges:             <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:             <none>
```
## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md) 
