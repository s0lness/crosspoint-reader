# personal-crosspoint

Firmware perso de ma liseuse Xteink X3 (ESP32-C3). Fork de [CrossPoint](https://github.com/crosspoint-reader/crosspoint-reader), base v1.5.0 (`upstream/master`), ce que l'appareil tournait avant les modifs.

## Changements par rapport à v1.5.0

- **Footnotes en long-press** : nouvelle valeur "Footnotes" pour Settings > Controls > Long-press Menu. Maintenir Confirm ~400 ms ouvre les notes de la page (saut direct s'il n'y en a qu'une, liste sinon, retour à la position sauvegardée depuis une note). Libère le bouton power pour Sleep.
- **Hints Up/Down** sur la liste de notes : les deux boutons de droite déplaçaient déjà la sélection mais n'affichaient rien.
- Deux gardes : le relâchement du long press ne sélectionne pas la première note de la liste, et le premier appui court après retour de la liste n'est pas avalé.

Détail dans le commit `feat(reader): Footnotes as a Long-press Menu function`.

## Build et flash (machine ARM64 Windows)

Pas de toolchain ESP32 sous Windows ARM64 : build dans WSL Ubuntu (pioarduino-core 6.1.19), source rsyncée car un clone git d'un worktree Windows échoue, et `sed -i 's/\r$//' scripts/jpegdec_patches/*.patch` obligatoire (le checkout CRLF casse `git apply`). Puis `pio run -e gh_release`.

Flash : `python -m esptool --chip esp32c3 --port COM7 --baud 921600 write-flash 0x10000 firmware.bin`.

Piège : deux slots OTA (app0 0x10000, app1 0x650000). Si otadata pointe app1, un flash à 0x10000 est invisible. Fix : `erase-region 0xe000 0x2000` puis écrire le binaire aux deux offsets. Réglages (NVS) et carte SD intacts.
