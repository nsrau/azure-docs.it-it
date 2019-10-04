---
title: "Soluzione VMware di Azure di CloudSimple: configurare vCenter sul cloud privato per l'automazione di vRealize"
description: Viene descritto come configurare un server VMware vCenter nel cloud privato CloudSimple come endpoint per l'automazione di vRealize VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642396"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configurare vCenter nel cloud privato per l'automazione vRealize VMware

È possibile configurare un server VMware vCenter nel cloud privato CloudSimple come endpoint per l'automazione di vRealize VMware.

## <a name="before-you-begin"></a>Prima di iniziare

Completare queste attività prima di configurare il server vCenter:

* Configurare una [connessione VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra l'ambiente locale e il cloud privato.
* [Configurare l'invio DNS di richieste DNS locali](on-premises-dns-setup.md) ai server DNS per il cloud privato.
* Inviare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per creare un utente amministratore IaaS di automazione vRealize con il set di autorizzazioni elencate nella tabella seguente.

| Valore attributo | Autorizzazioni |
------------ | ------------- |  
| Archivio dati |  Alloca spazio <br> Sfoglia archivio dati |
| Cluster di archivio dati | Configurare un cluster di archivio dati |
| Cartella | Crea cartella <br>Elimina cartella |
| Global |  Gestire attributi personalizzati<br>Imposta attributo personalizzato |
| Rete | Assegna rete |
| Autorizzazioni | Modificare le autorizzazioni |
| Risorsa | Assegnare una macchina virtuale al pool di risorse<br>Esegui la migrazione della macchina virtuale spenta<br>Esegui la migrazione della macchina virtuale accesa |
| Inventario macchina virtuale |  Crea da esistente<br>Crea nuova<br>Sposta<br>Remove | 
| Interazione tra macchine virtuali |  Configurare supporti CD<br>Interazione tra console<br>Connessione del dispositivo<br>Spegni<br>Accensione<br>Reimposta<br>Sospendi<br>Installazione degli strumenti | 
| Configurazione macchina virtuale |  Aggiungi disco esistente<br>Aggiungi nuovo disco<br>Aggiungi o Rimuovi<br>Rimuovi disco<br>Avanzate<br>Cambia Conteggio CPU<br>Cambia risorsa<br>Estendi disco virtuale<br>Rilevamento modifiche disco<br>Memoria<br>Modificare le impostazioni del dispositivo<br>Rinomina<br>Set Annotation (versione 5,0 e successive)<br>Impostazioni<br>Selezione host file |
| Provisioning |  Personalizza<br>Clona modello<br>Clona macchina virtuale<br>Modello di distribuzione<br>Leggi le specifiche di personalizzazione |
| Stato macchina virtuale | Crea snapshot<br>Rimuovi snapshot<br>Ripristina snapshot |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installare automazione di vRealize nell'ambiente locale

1. Accedere al dispositivo vRealize Automation IaaS server come amministratore di IaaS che CloudSimple il supporto creato automaticamente.
2. Distribuire un agente vSphere per l'endpoint di automazione vRealize.
    1. Passare a https://*LRV-URL*: 5480/Installer, dove *LRV-URL* è l'URL usato per accedere all'interfaccia utente di amministrazione di automazione vRealize.
    2. Per scaricare il programma di installazione, fare clic sul **programma di installazione di IaaS** .<br>
    La convenzione di denominazione per il file del programma di installazione è setup_*LRV-URL*@5480.exe.
    3. Eseguire il programma di installazione. Nella schermata iniziale fare clic su **Avanti**.
    4. Accettare il contratto di licenza e fare clic su **Avanti**.
    5. Fornire le informazioni di accesso, fare clic su **Accept certificate**, quindi fare clic su **Next**.
    ![credenziali LRV](media/configure-vra-endpoint-login.png)
    6. Selezionare **installazione personalizzata** e **agenti proxy** , quindi fare clic su **Avanti**.
    ![tipo di installazione LRV](media/configure-vra-endpoint-install-type.png)
    7. Immettere le informazioni di accesso al server IaaS e fare clic su **Avanti**. Se si utilizza Active Directory, immettere il nome utente in formato **dominio\utente** . In caso contrario **user@domain** , usare format.
    ![informazioni di accesso di LRV](media/configure-vra-endpoint-account.png)
    8. Per le impostazioni proxy, immettere **vSphere** per **tipo di agente**. Immettere un nome per l'agente.
    9. Immettere il nome di dominio completo del server IaaS nell' **host del servizio di gestione** e nei campi host del **servizio Web di gestione modelli** . Fare clic su **test** per testare la connessione per ognuno dei valori FQDN. Se il test ha esito negativo, modificare le impostazioni DNS in modo da risolvere il nome host del server IaaS.
    10. Immettere un nome per l'endpoint server vCenter per il cloud privato. Registrare il nome da utilizzare in un secondo momento nel processo di configurazione.

        ![proxy di installazione di LRV](media/configure-vra-endpoint-proxy.png)

    11. Fare clic su **Avanti**.
    12. Fare clic su **Installa**.

## <a name="configure-the-vsphere-agent"></a>Configurare l'agente vSphere

1. Passare a https://*LRV-URL*/vCAC e accedere come **ConfigurationAdmin**.
2. Selezionare endpoint dell' **infrastruttura** >  > endpoint.
3. Selezionare **nuovo** > virtualevSphere > .
4. Immettere il nome dell'endpoint vSphere specificato nella procedura precedente.
5. Per **Indirizzo**immettere l'URL di server vCenter del cloud privato nel formato https://*vCenter-FQDN*/SDK, dove *vCenter-FQDN* è il nome del server vCenter.
6. Immettere le credenziali per l'utente amministratore di vRealize Automation IaaS che è stato creato da CloudSimple.
7. Fare clic su **Test connessione** per convalidare le credenziali utente. Se il test ha esito negativo, verificare l'URL, le informazioni sull'account e il nome e il test dell' [endpoint](#verify-the-endpoint-name) .
8. Dopo un test riuscito, fare clic su **OK** per creare l'endpoint vSphere.
    ![accesso alla configurazione dell'endpoint LRV](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verificare il nome dell'endpoint

Per identificare il nome dell'endpoint del server vCenter corretto, procedere come segue:

1. Aprire un prompt dei comandi nell'appliance IaaS.
2. Passare alla directory C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, dove *Agent-Name* è il nome assegnato all'endpoint del server vCenter.
3. Eseguire il seguente comando.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

L'output è simile al seguente. Il valore del `managementEndpointName` campo è il nome dell'endpoint.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
