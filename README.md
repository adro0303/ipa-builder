# ipa-builder

Herramienta reutilizable para generar un `.ipa` de iOS **sin firmar** a partir de cualquier
proyecto Expo / React Native tuyo, compilando en la nube (GitHub Actions, runner macOS).
El `.ipa` resultante se instala con Sideloadly / AltServer usando tu Apple ID (que ya lo
firma en ese paso, así que no hace falta pagar la cuenta de Apple Developer).

## Configuración inicial (una sola vez)

El workflow necesita permiso para leer tus otros repos (aunque sean privados) al hacer
`checkout`. Para eso:

1. Ve a https://github.com/settings/tokens?type=beta y pulsa **Generate new token**
   (fine-grained).
2. Ponle un nombre, por ejemplo `ipa-builder-checkout`.
3. En **Repository access**, elige **Only select repositories** y marca los repos que
   quieras poder compilar (por ejemplo `mcqueens-web`). Puedes volver a editar el token
   más adelante para añadir repos nuevos.
4. En **Permissions → Repository permissions**, pon **Contents: Read-only**. No hace
   falta nada más.
5. Genera el token y cópialo (solo se muestra una vez).
6. En tu terminal, dentro de esta carpeta, ejecuta:
   ```
   gh secret set CROSS_REPO_TOKEN --repo adro0303/ipa-builder
   ```
   y pega el token cuando te lo pida.

## Uso (cada vez que quieras un .ipa nuevo)

```
gh workflow run build-ipa.yml --repo adro0303/ipa-builder \
  -f repo=adro0303/mcqueens-web \
  -f ref=main \
  -f app_dir=app-ios \
  -f ipa_name=McQueens-unsigned
```

- `repo`: el repo (owner/nombre) donde está el código de la app que quieres compilar.
- `ref`: rama, tag o commit (por defecto `main`).
- `app_dir`: carpeta dentro de ese repo donde está el `package.json`/`app.json` del
  proyecto Expo. Pon `.` si está en la raíz del repo.
- `ipa_name`: nombre que le quieres dar al `.ipa` resultante.

También puedes lanzarlo desde la web: pestaña **Actions** de este repo → **Build unsigned
iOS IPA** → **Run workflow**, y rellenas los mismos campos.

Cuando termine (tarda 10-15 min), baja a la sección **Artifacts** de esa ejecución y
descarga el `.ipa`. Caduca a los 14 días.

## Notas

- Necesitas que el repo objetivo tenga un `app.json`/`eas.json` de Expo válido con
  `ios.bundleIdentifier` puesto.
- El `.ipa` no está firmado por Apple: eso es intencional, Sideloadly/AltServer lo
  firman ellos con tu Apple ID gratis al instalarlo.
- Si el token del repo objetivo caduca o no tiene acceso, el primer paso
  (`Checkout target project`) fallará con un error de permisos — revisa el token en
  https://github.com/settings/tokens?type=beta.
