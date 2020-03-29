---
title: Gestire i vincoli del contesto di sicurezza in Azure Red Hat OpenShift Documenti Microsoft
description: Vincoli del contesto di sicurezza per gli amministratori del cluster Azure Red Hat OpenShiftSecurity context constraints for Azure Red Hat OpenShift cluster administrators
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 24163adcec889e9eedc2362ff1f01f00257a98f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063184"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Gestire i vincoli del contesto di sicurezza in Azure Red Hat OpenShiftManage security context constraints in Azure Red Hat OpenShift 

I vincoli del contesto di sicurezza (SCC) consentono agli amministratori del cluster di controllare le autorizzazioni per i pod. Per ulteriori informazioni su questo tipo di API, vedere la [documentazione sull'architettura per SCC](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). È possibile gestire gli SCC nell'istanza come normali oggetti API usando l'interfaccia della riga di comando.

## <a name="list-security-context-constraints"></a>Elencare i vincoli del contesto di sicurezzaList security context constraints

Per ottenere un elenco corrente di SCC, utilizzare questo comando: 

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

## <a name="examine-an-object-for-security-context-constraints"></a>Esaminare un oggetto per i vincoli del contesto di sicurezzaExamine an object for security context constraints

Per esaminare un determinato `oc get` `oc describe`SCC, utilizzare , , o `oc edit`.  Ad esempio, per esaminare il **ccg con restrizioni,** utilizzare questo comando:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md) 
