Dopo avere creato un certificato radice autofirmato, esportare il file con estensione cer con la chiave pubblica (non la chiave privata) del certificato radice. Successivamente, caricare il file in Azure. La procedura seguente consente di esportare il file con estensione .cer per il certificato radice autofirmato:

1. Per ottenere un file con estensione cer dal certificato, aprire **Gestire i certificati utente**. Individuare il certificato radice autofirmato, in genere in "Certificati - Utente corrente\Personale\Certificati" e fare clic con il pulsante destro del mouse. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.

  ![Esportazione](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Nella procedura guidata fare clic su **Avanti**.

  ![Esportare il certificato](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.

  ![Non esportare la chiave privata](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**.

  ![Codifica Base 64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

  ![Sfoglia](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Fare clic su **Fine** per esportare il certificato.

  ![Finish](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Il certificato è stato esportato correttamente.

  ![Success](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Il certificato esportato è simile al seguente:

  ![Esportato](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se si apre il certificato esportato usando il Blocco note, viene visualizzato un certificato simile a questo esempio. La sezione in blu contiene le informazioni caricate in Azure. Se si apre il certificato con il Blocco note e non è simile all'esempio seguente, in genere significa che non è stato esportato usando il formato di codifica Base 64 X.509(.CER). Se si vuole usare un editor di testo diverso, è anche utile sapere che alcuni editor possono introdurre una formattazione imprevista in background. Ciò può creare problemi quando il testo viene caricato da questo certificato in Azure.

  ![Apri con il Blocco note](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)