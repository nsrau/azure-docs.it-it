---
title: Gestire le registrazioni dei dispositivi con il portale di Azure | Microsoft Docs
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
ms.openlocfilehash: 06cc215e5c4087c7a38937de10eaa066037ac444
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Come gestire le registrazioni dei dispositivi con il portale di Azure

La *registrazione dei dispositivi* crea un record di un singolo dispositivo o di un gruppo di dispositivi registrato in un dato punto con il Servizio Device Provisioning in hub IoT di Azure. Il record di registrazione contiene la configurazione iniziale desiderata per i dispositivi come parte della registrazione, incluso l'hub IoT desiderato. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning.


## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi

Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo di registrazioni** è una voce relativa a un gruppo di dispositivi che condividono un meccanismo di attestazione comune dei certificati X.509, firmato dallo stesso certificato di firma, che può essere il [certificato radice](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o il [certificato intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), usato per generare il certificato del dispositivo sul dispositivo fisico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Si noti che è possibile registrare solo i dispositivi che usano il meccanismo di attestazione X.509 come *gruppi di registrazione*. 

    È possibile creare un gruppo di registrazioni per un gruppo di dispositivi nel portale usando la procedura seguente:

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Fare clic sul pulsante **Aggiungi** nella parte superiore e inserire le informazioni necessarie per la voce dell'elenco di registrazione. Caricare il certificato radice per il gruppo di dispositivi. 
    5. Fare clic su **Save**. Dopo avere creato il gruppo di registrazione, sotto la scheda **Enrollment Groups** (Gruppi di registrazione) verrà visualizzato il nome del gruppo. 

        ![Gruppo di registrazione nel portale](./media/how-to-manage-enrollments/group-enrollment.png)

    
* Una **registrazione individuale** è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

    È possibile creare una registrazione singola nel portale mediante la procedura seguente:

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Fare clic sul pulsante **Aggiungi** in alto. 
    5. Selezionare il meccanismo di sicurezza per il dispositivo e immettere le informazioni necessarie per la voce dell'elenco di registrazione. Caricare un certificato firmato se il dispositivo implementa X.509. 
    6. Fare clic su **Save**. Dopo avere creato il gruppo di registrazione, sotto la scheda **Individual Enrollments** (Registrazioni singole) verrà visualizzato il nome del gruppo. 

        ![Registrazione singola sul portale](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione
È possibile aggiornare una voce di registrazione esistente sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
2. Passare alla voce di registrazione che si desidera modificare. Selezionando la voce, si aprirà un riepilogo dei dati per la registrazione del dispositivo. 
3. In questa pagina è possibile modificare gli elementi diversi dal tipo protezione e dalle credenziali, ad esempio l'hub IoT a cui il dispositivo dovrà essere collegato e l'ID del dispositivo. È anche possibile modificare lo stato iniziale del dispositivo gemello. 
4. Al termine, fare clic su **Salva** per aggiornare la registrazione del dispositivo. 

    ![Aggiornare una registrazione sul portale](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Rimuovere una registrazione del dispositivo
Nei casi in cui non è necessario eseguire il provisioning dei dispositivi in alcun hub IoT, è possibile rimuovere la voce di registrazione correlata sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
2. Individuare e selezionare la voce di registrazione che si desidera modificare. 
3. Fare clic su **Elimina** nella parte superiore, quindi selezionare **Sì** quando viene richiesto di confermare. 
5. Una volta completata l'azione, la voce apparirà rimossa dall'elenco delle registrazioni dei dispositivi. 
 
    ![Rimuovere una registrazione dal portale](./media/how-to-manage-enrollments/remove-enrollment.png)



