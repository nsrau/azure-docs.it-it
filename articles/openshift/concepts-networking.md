---
title: Concetti - Diagramma di rete per Azure Red Hat su OpenShift 4
description: Diagramma di rete e panoramica delle funzionalità di rete di Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419972"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Funzionalità di rete di Azure Red Hat su OpenShift 4

Questa guida offre una panoramica delle funzionalità di rete di Azure Red Hat sui cluster OpenShift 4, oltre a un diagramma e a un elenco di endpoint importanti.

Per altre informazioni sui principali concetti di rete di OpenShift, vedere la [documentazione relativa alle funzionalità di rete di Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Concetti di rete in Azure Red Hat OpenShift

![Diagramma di rete di Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Quando si distribuisce Azure Red Hat in OpenShift 4, l'intero cluster è contenuto in una rete virtuale. All'interno di questa rete virtuale, i nodi master e i nodi di lavoro si trovano ognuno nella propria subnet. Ogni subnet usa un servizio di bilanciamento del carico interno e pubblico.

## <a name="explanation-of-endpoints"></a>Descrizione degli endpoint

Di seguito sono elencati gli endpoint importanti in un cluster di Azure Red Hat OpenShift.

* **aro-pls**
    * È un endpoint di collegamento privato di Azure, usato dai tecnici responsabili dell'affidabilità dei siti Microsoft e Red Hat per gestire il cluster.
* **aro-internal-lb**
    * Questo endpoint bilancia il carico del traffico verso il server API. Per questo servizio di bilanciamento del carico i nodi master si trovano nel pool back-end.
* **aro-public-lb**
    * Se l'API è pubblica, questo endpoint instrada e bilancia il carico del traffico verso il server API. L'endpoint assegna un IP in uscita pubblico che consente ai master di accedere ad Azure Resource Manager e restituire report sull'integrità del cluster.
* **aro-internal**
    * Questo endpoint bilancia il carico del traffico dei servizi interni. Per questo servizio di bilanciamento del carico i nodi di lavoro si trovano nel pool back-end.
    * Questo servizio di bilanciamento del carico non viene creato per impostazione predefinita. Viene creato quando si crea un servizio di tipo LoadBalancer con le annotazioni corrette. Ad esempio: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Criteri di rete (in ingresso)**
    * Supportati come parte dell'SDN OpenShift
    * Abilitati per impostazione predefinita, applicati dai clienti
    * Conformi con NetworkPolicy V1, tuttavia i tipi In uscita e IPBlock non sono ancora supportati
    * **aro**
    * Questo endpoint bilancia il carico del traffico verso il server API. Per questo servizio di bilanciamento del carico i nodi master si trovano nel pool back-end.
  * **aro-internal-lb**
    * Questo endpoint viene usato per il traffico pubblico. È il percorso del traffico in ingresso quando si crea un'applicazione e una route.
    * Questo servizio di bilanciamento del carico copre anche la connettività Internet in uscita da qualsiasi pod in esecuzione nei nodi di lavoro tramite le regole in uscita di Azure Load Balancer.
        * Attualmente le regole in uscita non sono configurabili. Allocano le porte TCP 1.024 a ogni nodo.
        * DisableOutboundSnat non è configurato nelle regole di bilanciamento del carico, quindi i pod potrebbero ottenere come IP in uscita qualsiasi IP pubblico configurato in questo servizio Azure Load Balancer.
        * Come conseguenza dei due punti precedenti, l'unico modo per aggiungere porte SNAT temporanee consiste nell'aggiungere servizi LoadBalancer pubblici ad ARO.
* **Criteri di rete (in uscita)**
    * I criteri in uscita sono supportati tramite la funzionalità del firewall in uscita di OpenShift.
    * Un solo criterio per spazio dei nomi/progetto.
    * I criteri in uscita non sono supportati nello spazio dei nomi predefinito.
    * Le regole dei criteri in uscita vengono valutate in ordine (dalla prima all'ultima).
    * **aro-outbound-pip**
        * Questo endpoint funge da IP pubblico (PIP) per i nodi di lavoro.
        * Questo endpoint consente ai servizi di aggiungere a un elenco di indirizzi consentiti un indirizzo IP specifico proveniente da un cluster di Azure Red Hat OpenShift.
* **aro-node-nsg**
    * Quando si espone un servizio, l'API crea una regola in questo gruppo di sicurezza di rete in modo che il traffico possa raggiungere i nodi.
    * Per impostazione predefinita, questo gruppo di sicurezza di rete accetta tutto il traffico in uscita. Attualmente il traffico in uscita può essere limitato solo al piano di controllo di Azure Red Hat OpenShift.
* **aro-controlplane-nsg**
    * Questo endpoint consente al traffico solo di entrare attraverso la porta 6443 per raggiungere i nodi master.
* **Registro Azure Container**
    * È un registro contenitori fornito e usato da Microsoft internamente.
        * Questo registro fornisce le immagini della piattaforma host e i componenti cluster. Un esempio sono i contenitori di monitoraggio o di registrazione.
        * Non destinato all'uso da parte degli utenti di Azure Red Hat OpenShift.  
        * Sola lettura.
        * Le connessioni a questo registro si verificano sull'endpoint di servizio (connettività interna tra i servizi di Azure).
        * Per impostazione predefinita, questo registro interno non è disponibile all'esterno del cluster.
* **Collegamento privato**
    * Consente la connettività di rete dal piano di gestione in un cluster per la gestione del cluster.
    * Consente ai tecnici responsabili dell'affidabilità dei siti Microsoft e Red Hat di gestire il cluster.

## <a name="networking-basics-in-openshift"></a>Nozioni fondamentali sulla rete in OpenShift

OpenShift Software Defined Networking (SDN) viene usato per configurare una rete di overlay tramite Open vSwitch (OVS), un'implementazione di OpenFlow basata sulla specifica CNI (Container Network Interface). SDN supporta diversi plug-in e Criteri di rete è il plug-in usato in Azure Red Hat su OpenShift 4. Tutte le comunicazioni di rete sono gestite da SDN, quindi non sono necessarie route aggiuntive sulle reti virtuali per ottenere la comunicazione da pod a pod.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Specifiche di rete di Azure Red Hat OpenShift

Le funzionalità seguenti sono specifiche di Azure Red Hat OpenShift:
* L'uso della rete virtuale personale è supportato.
* I CIDR di rete per pod e servizi sono configurabili.
* I nodi e i master si trovano in subnet diverse.
* Le subnet di rete virtuale di nodi e master devono avere dimensioni minime di /27.
* Il CIDR pod deve avere dimensioni minime di /18 (la rete pod ha indirizzi IP non instradabili e viene usata solo all'interno di OpenShift SDN).
* A ogni nodo è allocata una subnet di dimensioni /23 (512 IP) per i pod. Questo valore non può essere modificato.
* Non è possibile collegare un pod a più reti.
* Non è possibile configurare un indirizzo IP statico in uscita (questa è una funzionalità OpenShift. Per altre informazioni, vedere [Configurazione di indirizzi IP in uscita](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Impostazioni di rete

Le impostazioni di rete seguenti sono disponibili in Azure Red Hat OpenShift 4:

* **API Visibility** (Visibilità API): imposta la visibilità dell'API quando si esegue il [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * "Public" (Pubblica): il server API è accessibile da reti esterne.
    * "Private" (Privata): al server API è assegnato un IP privato dalla subnet master, accessibile solo tramite reti connesse (reti virtuali con peering, altre subnet nel cluster). Verrà creata una zona DNS privata per conto del cliente.
* **Ingress Visibility** (Visibilità in ingresso): imposta la visibilità dell'API quando si esegue il [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * Per impostazione predefinita, le route pubbliche vengono indirizzate al servizio Load Balancer Standard di Azure (questa impostazione può essere modificata).
    * Per impostazione predefinita, le route private vengono indirizzate al servizio di bilanciamento del carico interno (questa impostazione può essere modificata).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
I gruppi di sicurezza di rete verranno creati nel gruppo di risorse del nodo, che è bloccato. I gruppi di sicurezza di rete vengono assegnati direttamente alle subnet, non nelle schede di interfaccia di rete del nodo. I gruppi di sicurezza di rete non sono modificabili, ossia non si dispone delle autorizzazioni per modificarli. 

Tuttavia, con un server API visibile pubblicamente non è possibile creare gruppi di sicurezza di rete e assegnarli alle schede di interfaccia di rete.

## <a name="domain-forwarding"></a>Inoltro del dominio
Azure Red Hat OpenShift usa CoreDNS. È possibile configurare l'inoltro del dominio. Per altre informazioni, vedere la documentazione sull'[uso dell'inoltro DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator).

Attualmente non è possibile usare il DNS personale nelle reti virtuali.


Per altre informazioni sul traffico in uscita e sulle funzionalità supportate da Azure Red Hat OpenShift per il traffico in uscita, vedere la documentazione sui [criteri di supporto](support-policies-v4.md).
