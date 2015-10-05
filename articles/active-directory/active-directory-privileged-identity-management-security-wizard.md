<properties
   pageTitle="Procedura guidata sulla sicurezza di Azure Privileged Identity Management"
   description="Quando si usa l'estensione Azure Privileged Identity Managment per la prima volta, viene visualizzata una procedura guidata sulla sicurezza. Questo articolo descrive i passaggi della procedura guidata."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Procedura guidata sulla sicurezza di Azure Privileged Identity Management

Alla prima esecuzione di Azure Privileged Identity Management, verrà visualizzata una procedura guidata che fornisce informazioni sui rischi di sicurezza delle identità con privilegi e su come usare Privileged Identity Management per ridurre i rischi.

La procedura guidata include tre sezioni: **GLI AMMINISTRATORI POSSONO CREARE SITUAZIONI DI RISCHIO, GESTISCI LA SUPERFICIE DI ATTACCO DEGLI AMMINISTRATORI** e **DEFINISCI IMPOSTAZIONI AMMINISTRATORI TEMPORANEI**. Ogni sezione fornisce una panoramica dei concetti e una spiegazione di alcune azioni da intraprendere.

Inizialmente, tutti gli amministratori globali saranno permanenti. Quando si fa clic su **GLI AMMINISTRATORI POSSONO CREARE SITUAZIONI DI RISCHIO**, viene visualizzato un elenco di ruoli di amministratore globale con l'indicazione di quelli attualmente disponibili.

Facendo clic su **GESTISCI LA SUPERFICIE DI ATTACCO DEGLI AMMINISTRATORI**, viene offerta la possibilità di impostare gli amministratori come temporanei, lasciarli impostati come permanenti o rimuoverli del tutto dal ruolo.

**DEFINISCI IMPOSTAZIONI AMMINISTRATORI TEMPORANEI** consente di determinare la durata dei privilegi di un amministratore temporaneo, abilitare le notifiche e richiedere l'autenticazione a più fattori.

## Impostare i ruoli di amministratore globale come temporanei o permanenti

Sono disponibili tre opzioni per modificare la finestra temporale di un amministratore globale.

1.  Fare clic sul pulsante **Imposta tutti come temporanei** per rendere temporanei tutti gli amministratori globali.

2.  Fare clic sul pulsante **Imposta tutti come permanenti** per rendere permanenti tutti gli amministratori globali.

3.  Selezionare **Mantieni come permanente**, **Imposta come temporaneo** o **Rimuovi da ruolo** per ogni amministratore globale.

4.  Fare clic su **OK**.

5.  Fare clic su **Submit**.

## Modificare il periodo di attivazione per un ruolo di amministratore globale

1.  Spostare il dispositivo di scorrimento relativo al **periodo di attivazione** verso sinistra o verso destra per aumentare o ridurre il periodo di attivazione. Il periodo di attivazione può essere di massimo 72 ore.

2.  Immettere il numero di ore nel campo relativo alle **ore** a destra del dispositivo di scorrimento.

## Abilitare le notifiche

Affinché gli amministratori possano ricevere posta elettronica quando vengono attivati i ruoli, abilitare le notifiche facendo clic sul pulsante **Abilita**. Questa funzionalità può anche essere disabilitata.

## Richiedere l'autenticazione a più fattori

Se si desidera che gli amministratori usino l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per l'accesso agli account e per richiedere un'estensione del proprio ruolo, abilitare questa funzionalità facendo clic sul pulsante **Abilita**. Questa funzionalità può anche essere disabilitata.

Per altre informazioni su MFA e PIM, fare clic qui. PLACEHOLDER: NEED LINK TO MFA DOC.

Selezionare i ruoli a cui verranno applicate le impostazioni. Fare clic su **OK**.

> [AZURE.WARNING]È importante a questo punto che siano presenti più amministratori della sicurezza. Se infatti un amministratore non è impostato come permanente, non è configurata l'autenticazione MFA per tale utente e scade l'assegnazione del ruolo, non sarà possibile amministrare PIM.

Fare clic su **OK** al termine delle operazioni.

Dopo aver apportato le modifiche, la procedura guidata non verrà più visualizzata. Sarà comunque possibile accedervi di nuovo facendo clic sul pulsante corrispondente in Gestisci identità.

## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->