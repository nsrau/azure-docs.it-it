
#### Per completare la configurazione minima del dispositivo StorSimple

1. Selezionare il dispositivo e fare clic su **Avvio rapido**. Fare clic su **Configurazione completa dispositivo** per avviare la configurazione guidata dispositivo.

2. Nella finestra di dialogo **Impostazioni di base** della configurazione guidata dispositivo, effettuare le seguenti operazioni:
  1. Fornire un **nome descrittivo** per il dispositivo. Il nome predefinito del dispositivo riflette informazioni quali il modello di dispositivo e il numero di serie. È possibile assegnare un nome descrittivo di un massimo di 64 caratteri per gestire il dispositivo.
  2. Impostare il **fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.
  3. In **Impostazioni DNS**, fornire un indirizzo per **Server DNS secondario**. Se si utilizza IPv6, il campo verrà popolato in base al prefisso IPv6 fornito nell'interfaccia di Windows PowerShell. Se non è configurato il server DNS secondario, non sarà consentito salvare la configurazione del dispositivo.
  4. Nelle interfacce iSCSI abilitate, abilitare almeno una rete per iSCSI. Almeno un'interfaccia di rete deve essere abilitata per il cloud e un'interfaccia deve essere abilitata per iSCSI. DATA 0 è abilitata automaticamente per il cloud.
 
      ![Impostazioni di base della configurazione minima del dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Fare clic sull’icona di freccia. ![Icona di freccia di StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. Nella finestra di dialogo **Interfacce di rete**, fornire gli indirizzi IP fissi per Controller 0 e Controller 1. **Gli indirizzi IP fissi del controller devono essere IP disponibili all’interno della subnet accessibile mediante l’indirizzo IP del dispositivo.** Se l’interfaccia DATA 0 è stata configurata per IPv4, gli indirizzi IP fissi devono essere forniti nel formato IPv4. Se è stato fornito un prefisso per la configurazione IPv6, gli indirizzi IP fissi verranno popolati automaticamente in questi campi.


    ![Interfacce di rete della configurazione minima del dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    Gli indirizzi IP fissi per il controller vengono utilizzati per l’installazione degli aggiornamenti nel dispositivo e pertanto gli IP fissi devono essere instradabili e in grado di connettersi a Internet. È possibile verificare che gli indirizzi IP fissi per il controller siano instradabili usando il cmdlet [Test-HcsmConnection][Test]. Nell'esempio seguente viene illustrato come gli indirizzi IP fissi per il controller vengono instradati a Internet e possono accedere al server di Microsoft Update.

     ![Test-HcsmConnection con indirizzi IP instradabili](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

5. Fare clic sull’icona del segno di spunta ![Icona del segno di spunta di StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png). Si ritornerà alla pagina **Avvio rapido** del dispositivo.

 >[AZURE.NOTE]È possibile modificare tutte le altre impostazioni del dispositivo in qualsiasi momento effettuando l’accesso alla pagina **Configura**.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx

<!---HONumber=July15_HO4-->