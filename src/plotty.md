# Discord Bot

Plotty ist ein [selbstgeschriebener Discord-Bot](https://github.com/zekroTJA/plotty), welcher genutzt werden kann um sich direkt über Discord auf dem Server zu Whitelisten oder um sich ein Grundstück via WorldGuard zu registrieren und zu managen.

![](/assets/plotty-discord-profile.png)

## Commands

Eine Liste aller Commands des Bots kann über die Command-Palette aufgerufen werden. Tipp dafür einfach `/` in die Textbox ein und Klick auf das Logo von Plotty auf der linken Seite.

![](/assets/plotty-commands.png)

### `/bind <minecraft-username>`

Verbinde deinen Discord-Account mit deinem Minecraft-Nutzer. Dies setzt dich auch automatisch auf die Whitelist des Servers. Einige Befehle des Bots - wie z.B. die `/region` Commands - können erst nach dem Verknüpfen des Discord- mit dem Minecraft-Accounts benutzt werden!

| Parameter            | Optional | Beschreibung                  | Beispiel   |
| -------------------- | -------- | ----------------------------- | ---------- |
| `minecraft-username` | Nein     | Dein Nutzername in Minecraft. | `zekroTJA` |

### `/region create <pos1-x> <pos1-z> <pos2-x> <pos2-z> (<world>)`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Erstellt eine geschützte Region innerhalb der angegebenen Koordinaten und in der angegebenen Welt.

| Parameter | Optional                  | Beschreibung                                                                                          | Beispiel |
| --------- | ------------------------- | ----------------------------------------------------------------------------------------------------- | -------- |
| `pos1-x`  | Nein                      | X-Koordinate der oberen, linken Ecke des Grundstücks.                                                 | `420`    |
| `pos1-z`  | Nein                      | Z-Koordinate der oberen, linken Ecke des Grundstücks.                                                 | `69`     |
| `pos2-x`  | Nein                      | X-Koordinate der unteren, rechten Ecke des Grundstücks.                                               | `520`    |
| `pos2-z`  | Nein                      | Z-Koordinate der unteren, rechten Ecke des Grundstücks.                                               | `169`    |
| `world`   | Ja<br/>(default: `world`) | Welt, in der das Grundstück erstellt werden soll. Werte können sein `world`, `nether` oder `the_end`. | `nether` |

> 💡 **Tipp**: Du kannst die [online Map](https://mc.zekro.de) des Servers nutzen, um die Koordinaten deines Grundstücks zu bestimmen.
>
> ![](/assets/position-guide.png)

### `/region list`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Listet alle deiner Grundstücke auf.

### `/region redefine <plotname> <pos1-x> <pos1-z> <pos2-x> <pos2-z> (<world>)`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Ändert die Position und Größe eines bereits bestehenden Grundstücks.

| Parameter  | Optional                  | Beschreibung                                                                                          | Beispiel          |
| ---------- | ------------------------- | ----------------------------------------------------------------------------------------------------- | ----------------- |
| `plotname` | Nein                      | Name des zu ändernden Grundstücks.                                                                    | `zekrotja_plot_1` |
| `pos1-x`   | Nein                      | X-Koordinate der oberen, linken Ecke des Grundstücks.                                                 | `420`             |
| `pos1-z`   | Nein                      | Z-Koordinate der oberen, linken Ecke des Grundstücks.                                                 | `69`              |
| `pos2-x`   | Nein                      | X-Koordinate der unteren, rechten Ecke des Grundstücks.                                               | `520`             |
| `pos2-z`   | Nein                      | Z-Koordinate der unteren, rechten Ecke des Grundstücks.                                               | `169`             |
| `world`    | Ja<br/>(default: `world`) | Welt, in der das Grundstück erstellt werden soll. Werte können sein `world`, `nether` oder `the_end`. | `nether`          |

### `/region member add <plotname> <username>`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Fügt einen Member zu deinem Grundstück hinzu.

| Parameter  | Optional | Beschreibung                                                                                 | Beispiel          |
| ---------- | -------- | -------------------------------------------------------------------------------------------- | ----------------- |
| `plotname` | Nein     | Der name des zu löschenden Plots.                                                            | `zekrotja_plot_1` |
| `username` | Nein     | Minecraft-Username des Spielers, den du als Member zu deinem Grundstück hinzufügen möchtest. | `luxtracon`       |

### `/region member remove <plotname> <username>`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Entfernt einen Member von deinem Grundstück.

| Parameter  | Optional | Beschreibung                                                                                 | Beispiel          |
| ---------- | -------- | -------------------------------------------------------------------------------------------- | ----------------- |
| `plotname` | Nein     | Der name des zu löschenden Plots.                                                            | `zekrotja_plot_1` |
| `username` | Nein     | Minecraft-Username des Spielers, den du als Member von deinem Grundstück entfernen möchtest. | `luxtracon`       |

### `/region delete <plotname>`

> ℹ️ Benötigt Verknüpfung zwischen Discord- und Minecraft-Account (siehe [`/bind`](#bind-minecraft-username)).

Löscht eine deiner Regionen, welche mit dem Namen der Region angegeben wird.

| Parameter  | Optional | Beschreibung                      | Beispiel          |
| ---------- | -------- | --------------------------------- | ----------------- |
| `plotname` | Nein     | Der name des zu löschenden Plots. | `zekrotja_plot_1` |
