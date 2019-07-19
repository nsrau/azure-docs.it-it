---
title: Modello di distribuzione classica di Azure | Microsoft Docs
description: Modello di distribuzione classica di Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234908"
---
# <a name="classic-deployment-model"></a>Modello di distribuzione classica

Il modello di distribuzione classica è la modalità di distribuzione di Azure di generazione precedente impone un limite globale di quota di vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali. Il modello di distribuzione classica non è più consigliato ed è ora sostituito da Gestione risorse modello. Per ulteriori informazioni su questi due modelli di distribuzione e sui vantaggi offerti da Gestione risorse fare riferimento alla pagina Gestione risorse modello di distribuzione. Quando viene creata una nuova sottoscrizione, viene assegnata una quota predefinita di vCPU. Ogni volta che una nuova macchina virtuale deve essere distribuita usando il modello di distribuzione classica, la somma dell'utilizzo di vCPU nuovo ed esistente in tutte le aree non deve superare la quota vCPU approvata per il modello di distribuzione classica. Altre informazioni sulle quote nella [pagina sottoscrizione di Azure e limiti del servizio](https://aka.ms/quotalimits)

È possibile richiedere un aumento del limite di vCPU per il modello di distribuzione classica tramite il pannello Guida e supporto oppure il pannello utilizzi e quota nel portale.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando il pannello **Guida e supporto**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure. 

1. Da https://portal.azure.com scegliere **Guida e supporto**.

   ![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

      ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema scegliere **limiti per servizio e sottoscrizione (quote)** .

   ![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Seleziona newS Subscription](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **calcolo-VM (Core-vCPU) il limite di sottoscrizione aumenta** nell'elenco a discesa **tipo di quota** . 

   ![Seleziona tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. In **Dettagli problema**specificare informazioni aggiuntive per l'elaborazione della richiesta facendo clic su **Fornisci dettagli**.

   ![Specificare i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel pannello dei **Dettagli della quota** selezionare classico e selezionare una località.

   ![Dettagli quota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selezionare le **famiglie di SKU** che richiedono un aumento. 

   ![Famiglia SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

   ![Nuovi limiti](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Richiesta per serie di macchine virtuali vCPU aumento della quota a livello di sottoscrizione usando il pannello **utilizzo + quote**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure. 

1. In https://portal.azure.com , selezionare **Sottoscrizioni**.

   ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

   ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Il **limite di sottoscrizione Compute-VM (Cores-vCPU) aumenta** come tipo di virgolette. 

   ![Compilare i moduli](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. In **Dettagli problema**specificare informazioni aggiuntive per l'elaborazione della richiesta facendo clic su **Fornisci dettagli**.

   ![Specificare i dettagli](./media/resource-manager-core-quotas-request/provide-details.png)

7. Nel pannello dei **Dettagli della quota** selezionare classico e selezionare una località.

   ![Dettagli quota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selezionare le **famiglie di SKU** che richiedono un aumento. 

   ![Famiglia SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Immettere i nuovi limiti da applicare alla sottoscrizione. Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x". Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su **Salva e continua** nel pannello Dettagli quota per continuare con la creazione della richiesta di supporto.

   ![Nuovi limiti](./media/resource-manager-core-quotas-request/new-limits-classic.png)

