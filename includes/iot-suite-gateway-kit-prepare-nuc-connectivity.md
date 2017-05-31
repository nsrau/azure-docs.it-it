## <a name="prepare-your-intel-nuc"></a>Preparare Intel NUC

Per completare la configurazione dell'hardware, è necessario:

- Connettere Intel NUC all'alimentatore incluso nel kit.
- Connettere Intel NUC alla rete con un cavo Ethernet.

La configurazione hardware del dispositivo gateway Intel NUC è completata.

### <a name="sign-in-and-access-the-terminal"></a>Accedere al terminale

Per accedere all'ambiente di un terminale in Intel NUC sono disponibili due opzioni:

- Se a Intel NUC sono connessi una tastiera e un monitor, è possibile accedere direttamente alla shell. Le credenziali predefinite sono il nome utente **root** e la password **root**.

- Accedere alla shell in Intel NUC usando SSH dal computer desktop.

#### <a name="sign-in-with-ssh"></a>Accedere con SSH

Per accedere con SSH, è necessario l'indirizzo IP di Intel NUC. Se a Intel NUC sono connessi una tastiera e un monitor, usare il comando `ifconfig` per trovare l'indirizzo IP. In alternativa, connettersi al router per ottenere l'elenco degli indirizzi dei dispositivi nella rete.

Accedere con il nome utente **root** e la password **root**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Facoltativo: condividere una cartella in Intel NUC

Facoltativamente, è possibile condividere una cartella in Intel NUC con l'ambiente desktop. La condivisione di una cartella consente di modificare i file in Intel NUC usando l'editor di testo desktop preferito (ad esempio, [Visual Studio Code](https://code.visualstudio.com/) o [Sublime Text](http://www.sublimetext.com/)) invece di `nano` o `vi`.

Per condividere una cartella con Windows, configurare un server Samba in Intel NUC. In alternativa, usare il server SFTP in Intel NUC con un client SFTP nel computer desktop.
