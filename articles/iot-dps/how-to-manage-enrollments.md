---
title: Gestire le registrazioni dei dispositivi con il portale di Azure | Documenti Microsoft
description: Come gestire le registrazioni dei dispositivi per il servizio DPS nel portale di Azure
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b1d4e1e54d945c6edb0054da7b465b31de8c82a1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Come gestire le registrazioni dei dispositivi con il portale di Azure

La *registrazione dei dispositivi* crea un record di un singolo dispositivo o di un gruppo di dispositivi registrato in un dato punto con il Servizio Device Provisioning in hub IoT di Azure. Il record di registrazione contiene la configurazione iniziale desiderata per i dispositivi come parte della registrazione, incluso l'hub IoT desiderato. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning.


## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi

Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo registrazione** è una voce per un gruppo di dispositivi che condividono un meccanismo di attestazione comuni dei certificati x. 509, firmato dallo stesso certificato di firma, che può essere il [certificato radice](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate) o [certificato intermedio](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate), utilizzato per generare il certificato di dispositivo nel dispositivo fisico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Si noti che è possibile registrare solo i dispositivi che usano il meccanismo di attestazione X.509 come *gruppi di registrazione*. 

    È possibile creare un gruppo di registrazione nel portale per un gruppo di dispositivi usando la procedura seguente:

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Fare clic sul pulsante **Aggiungi** nella parte superiore e inserire le informazioni necessarie per la voce dell'elenco di registrazione. Caricare il certificato radice per il gruppo di dispositivi. 
    5. Fare clic su **Save**. Dopo avere creato il gruppo di registrazione, sotto la scheda **Enrollment Groups** (Gruppi di registrazione) verrà visualizzato il nome del gruppo. 

        ![Gruppo di registrazione nel portale](./media/how-to-manage-enrollments/group-enrollment.png)

    
* Una **registrazione individuale** è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

    È possibile creare una registrazione singoli nel portale di eseguire le operazioni seguenti:

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Fare clic sul pulsante **Aggiungi** in alto. 
    5. Selezionare il meccanismo di sicurezza per il dispositivo e immettere le informazioni necessarie per la voce dell'elenco di registrazione. Caricare un certificato firmato se il dispositivo implementa X.509. 
    6. Fare clic su **Save**. Dopo avere creato il gruppo di registrazione, sotto la scheda **Individual Enrollments** (Registrazioni singole) verrà visualizzato il nome del gruppo. 

        ![Registrazione singola sul portale](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione
È possibile aggiornare una voce di registrazione esistente nel portale di eseguire le operazioni seguenti:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
2. Passare alla voce di registrazione che si desidera modificare. Selezionando la voce, si aprirà un riepilogo dei dati per la registrazione del dispositivo. 
3. In questa pagina è possibile modificare gli elementi diversi dal tipo protezione e dalle credenziali, ad esempio l'hub IoT a cui il dispositivo dovrà essere collegato e l'ID del dispositivo. È anche possibile modificare lo stato iniziale del dispositivo gemello. 
4. Al termine, fare clic su **Salva** per aggiornare la registrazione del dispositivo. 

    ![Aggiornare una registrazione sul portale](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Rimuovere una registrazione del dispositivo
Nei casi in cui le periferiche non è necessario eseguirne il provisioning in un hub IoT, è possibile rimuovere la voce di registrazione correlati nel portale di eseguire le operazioni seguenti:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
2. Individuare e selezionare la voce di registrazione che si desidera modificare. 
3. Fare clic su **Elimina** nella parte superiore, quindi selezionare **Sì** quando viene richiesto di confermare. 
5. Una volta completata l'azione, la voce apparirà rimossa dall'elenco delle registrazioni dei dispositivi. 
 
    ![Rimuovere una registrazione dal portale](./media/how-to-manage-enrollments/remove-enrollment.png)



