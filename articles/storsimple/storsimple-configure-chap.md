<properties 
   pageTitle="Configurare CHAP per il dispositivo StorSimple | Microsoft Azure"
   description="Viene descritto come configurare Challenge Handshake Authentication Protocol su un dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="07/30/2015"
   ms.author="alkohli" />

# Configurare CHAP per il dispositivo StorSimple

In questa esercitazione viene illustrato come configurare CHAP per il dispositivo StorSimple.

CHAP è l'acronimo di Challenge Handshake Authentication Protocol. È uno schema di autenticazione utilizzato dal server per convalidare l'identità dei client remoti. La verifica si basa su una password condivisa o un segreto.

CHAP può essere unidirezionale o bidirezionale (reciproco). L'autenticazione CHAP unidirezionale avviene quando la destinazione autentica un iniziatore. Per l'autenticazione CHAP reciproca o inversa è necessario che la destinazione autentichi l'iniziatore e viceversa. È possibile implementare l'autenticazione dell'iniziatore senza autenticazione della destinazione. L’autenticazione della destinazione, tuttavia, può essere implementata solo se viene implementata anche l'autenticazione dell'iniziatore.

Come procedura consigliata, si raccomanda l’uso di CHAP per migliorare la sicurezza iSCSI.

>[AZURE.NOTE]Tenere presente che IPSEC non è attualmente supportato in dispositivi StorSimple.

Le impostazioni CHAP nel dispositivo StorSimple possono essere configurate nei modi seguenti:

- Autenticazione unidirezionale

- Autenticazione bidirezionale, reciproca o inversa

In ognuno di questi casi, è necessario configurare il portale di gestione del dispositivo e il software dell'iniziatore iSCSI del server. Nell'esercitazione seguente sono descritti i passaggi dettagliati per questa configurazione.

## Autenticazione unidirezionale

Nell’autenticazione unidirezionale, la destinazione autentica l'iniziatore. Questa autenticazione richiede che si configurino le impostazioni dell’iniziatore CHAP sul dispositivo StorSimple e il software dell’iniziatore iSCSI nell'host. Le procedure dettagliate per il dispositivo StorSimple e l’host di Windows sono descritte di seguito.

#### Per configurare il dispositivo per l'autenticazione unidirezionale

1. Nel portale di gestione, nella pagina **Dispositivi** fare clic su sulla scheda **Configura**.

    ![Iniziatore CHAP](./media/storsimple-configure-chap/IC740943.png)

2. Scorrere verso il basso in questa pagina e nella sezione **Iniziatore CHAP**:
													
	1. Specificare un nome utente per l'iniziatore CHAP.

	2. Specificare una password per l'iniziatore CHAP.

    > [AZURE.IMPORTANT]Il nome dell'utente CHAP deve contenere meno di 233 caratteri. La password CHAP deve essere compresa tra 12 e 16 caratteri. Se si prova a usare un nome utente o una password più lunga si verificherà un errore di autenticazione sull'host Windows.

#### Per configurare l'autenticazione unidirezionale nel server host di Windows

1. Nel server host di Windows, avviare l'iniziatore iSCSI.

2. Nella finestra **Proprietà iniziatore iSCSI**, effettuare le seguenti operazioni:
													
	1. Scegliere la scheda **Individuazione**.

		![Proprietà dell'iniziatore iSCSI](./media/storsimple-configure-chap/IC740944.png)

	2. Fare clic su **Individua portale**.

3. Nella finestra di dialogo **Individua portale destinazione**:
													
	1. Specificare l'indirizzo IP del dispositivo:

	3. Fare clic su **Advanced**.

		![Individua portale destinazione](./media/storsimple-configure-chap/IC740945.png)

4. Nella finestra di dialogo **Impostazioni avanzate**:
													
	1. Selezionare la casella di controllo **Attiva accesso CHAP**.

	2. Nel campo **Nome**, inserire il nome utente specificato per l'iniziatore CHAP nel portale di gestione.

	3. Nel campo **Segreto destinazione**, inserire la password specificata per l'iniziatore CHAP nel portale di gestione.

	4. Fare clic su **OK**.

		![Impostazioni avanzate - generale](./media/storsimple-configure-chap/IC740946.png)

5. Nella scheda **Destinazioni** della finestra **Proprietà iniziatore iSCSI**, lo stato del dispositivo deve essere visualizzato come **Connesso**.

    > [AZURE.IMPORTANT]Se si modifica il nome iSCSI, il nuovo nome verrà utilizzato per le nuove sessioni iSCSI. Le nuove impostazioni non vengono utilizzate per le sessioni esistenti fino a quando non si esegue la disconnessione e il nuovo accesso.

Per ulteriori informazioni sulla configurazione di CHAP nel server host di Windows, andare a [Ulteriori considerazioni](#additional-considerations).


## Autenticazione bidirezionale o reciproca

Nell'autenticazione bidirezionale la destinazione autentica l'iniziatore e poi l’iniziatore autentica la destinazione. A tal fine è necessario che l'utente configuri le impostazioni dell’iniziatore CHAP nonché le impostazioni di autenticazione CHAP inversa sul dispositivo e nel software dell’iniziatore iSCSI nell'host. Le procedure seguenti descrivono i passaggi per configurare l'autenticazione reciproca nel dispositivo e nell'host Windows.

#### Per configurare il dispositivo per l'autenticazione reciproca

1. Nel portale di gestione, nella pagina **Dispositivi** fare clic su sulla scheda **Configura**.

    ![Destinazione CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Scorrere verso il basso in questa pagina e nella sezione **Destinazione CHAP**:
													
	1. Fornire un **nome utente per l’autenticazione CHAP inversa** per il dispositivo.

	2. Fornire una **password per l’autenticazione CHAP inversa** per il dispositivo.

	3. Confermare la password.

3. Nella sezione **Iniziatore CHAP**:
												
	1. Specificare un **nome utente** per il dispositivo.

	1. Specificare una **password** per il dispositivo.

	3. Confermare la password.

4. Fare clic su **Save**. Verrà visualizzato un messaggio di conferma. Fare clic su **OK** per salvare le modifiche.

#### Per configurare l'autenticazione bidirezionale nel server host di Windows

1. Nel server host di Windows, avviare l'iniziatore iSCSI.

2. Nella finestra **Proprietà iniziatore iSCSI**, scegliere la scheda **Configurazione**.

3. Fare clic su **CHAP**.

4. Nella finestra di dialogo **Segreto autenticazione CHAP reciproca iniziatore iSCSI**:
													
	1. Immettere la **password dell'autenticazione CHAP inversa** configurata nel portale di gestione.

	2. Fare clic su **OK**.

		![Segreto autenticazione CHAP reciproca iniziatore iSCSI](./media/storsimple-configure-chap/IC740949.png)

5. Fare clic sulla scheda **Destinazioni**.

6. Scegliere il pulsante **Connetti**.

7. Nella finestra di dialogo **Connessione alla destinazione**, fare clic su **Avanzate**.

8. Nella finestra di dialogo **Proprietà avanzate**:
													
	1. Selezionare la casella di controllo **Attiva accesso CHAP**.

	2. Nel campo **Nome**, inserire il nome utente specificato per l'iniziatore CHAP nel portale di gestione.

	3. Nel campo **Segreto destinazione**, inserire la password specificata per l'iniziatore CHAP nel portale di gestione.

	4. Selezionare la casella di controllo **Esegui autenticazione reciproca**.

		![Impostazioni avanzate - Autenticazione reciproca](./media/storsimple-configure-chap/IC740950.png)

	5. Fare clic su **OK** per completare la configurazione di CHAP.

Per ulteriori informazioni sulla configurazione di CHAP nel server host di Windows, andare a [Ulteriori considerazioni](#additional-considerations).

## Considerazione aggiuntive

La funzionalità **Connessione rapida** non supporta le connessioni con CHAP attivato. Quando CHAP è attivato, assicurarsi di utilizzare il pulsante **Connetti** disponibile nella scheda **Destinazioni** per la connessione a una destinazione.

![Connessione a destinazione](./media/storsimple-configure-chap/IC740947.png)

Nella finestra di dialogo **Connessione alla destinazione** che viene visualizzata, selezionare la casella di controllo **Aggiungi connessione all'elenco Destinazioni preferite**. Ciò garantisce che ogni volta che il computer viene riavviato, viene effettuato un tentativo di ripristinare la connessione alle destinazioni preferite iSCSI.

## Errori durante la configurazione

Se la configurazione di CHAP non è corretta, è probabile che venga visualizzato un messaggio di errore **Errore di autenticazione**.

## Verifica della configurazione di CHAP

È possibile verificare che CHAP sia in uso completando i passaggi seguenti.

#### Per verificare la configurazione di CHAP

1. Fare clic su **Destinazioni preferite**.

2. Selezionare la destinazione per cui è abilitata l'autenticazione.

3. Fare clic su **Dettagli**.

    ![Proprietà iniziatore iSCSI - Destinazioni preferite](./media/storsimple-configure-chap/IC740951.png)

4. Nella finestra di dialogo **Dettagli destinazione preferita**, prendere nota della voce nel campo **Autenticazione**. Se la configurazione è corretta, il campo contiene la voce **CHAP**.

    ![Dettagli destinazione preferita](./media/storsimple-configure-chap/IC740952.png)

## Passaggi successivi

Ulteriori informazioni sulla [sicurezza di StorSimple](storsimple-security.md).

<!---HONumber=August15_HO6-->