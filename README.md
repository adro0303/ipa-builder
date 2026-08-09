# ipa-builder

Herramienta gratuita y de código abierto para generar un `.ipa` de iOS **sin firmar**
a partir de cualquier proyecto Expo / React Native, compilando en la nube con GitHub
Actions (runner macOS) — sin necesitar un Mac propio ni pagar la cuenta de pago de
Apple Developer (99$/año).

El `.ipa` resultante se instala en tu iPhone con [Sideloadly](https://sideloadly.io/)
o [AltStore](https://altstore.io/) usando tu Apple ID gratuito, que lo firma en el
propio paso de instalación. Esta herramienta solo se encarga de compilarlo.

## Por qué existe

Para probar tu propia app de iOS en tu propio dispositivo normalmente Apple te pide
una cuenta de pago si quieres compilar fuera de un Mac. Sideloadly/AltStore evitan
ese pago firmando la app ellos mismos con tu Apple ID gratis — pero necesitas
igualmente compilar el `.ipa` en algún sitio con Xcode. GitHub Actions te da minutos
de runner macOS gratis; este repo automatiza esa compilación para cualquier proyecto
Expo/React Native tuyo.

## Requisitos

- Una cuenta de GitHub con la [CLI `gh`](https://cli.github.com/) instalada y
  autenticada (`gh auth login`).
- Un proyecto Expo/React Native en un repo de GitHub (tuyo o al que tengas acceso),
  con `app.json` configurado (`ios.bundleIdentifier` puesto).
- Haz un fork de este repo (o cópialo) a tu propia cuenta.

## Configuración inicial (una sola vez)

Este workflow necesita permiso para leer el repo que quieres compilar (aunque sea
privado) al hacer `checkout`, usando un token propio (no el `GITHUB_TOKEN` por
defecto, que solo tiene acceso al repo donde vive el workflow).

1. Ve a https://github.com/settings/tokens?type=beta → **Generate new token**
   (fine-grained).
2. Ponle un nombre, por ejemplo `ipa-builder-checkout`.
3. En **Repository access**, elige **Only select repositories** y marca los repos
   que quieras poder compilar. Puedes volver a editar el token más adelante para
   añadir repos nuevos.
4. En **Permissions → Repository permissions**, pon **Contents: Read-only**. No
   hace falta nada más.
5. Genera el token y cópialo (solo se muestra una vez).
6. En tu terminal, dentro de tu copia de este repo, ejecuta:
   ```
   gh secret set CROSS_REPO_TOKEN --repo TU-USUARIO/ipa-builder
   ```
   y pega el token cuando te lo pida.

## Uso (cada vez que quieras un .ipa nuevo)

```
gh workflow run build-ipa.yml --repo TU-USUARIO/ipa-builder \
  -f repo=TU-USUARIO/tu-app \
  -f ref=main \
  -f app_dir=. \
  -f ipa_name=mi-app-unsigned
```

- `repo`: el repo (owner/nombre) donde está el código de la app que quieres compilar.
- `ref`: rama, tag o commit (por defecto `main`).
- `app_dir`: carpeta dentro de ese repo donde está el `package.json`/`app.json` del
  proyecto Expo. Pon `.` si está en la raíz del repo.
- `ipa_name`: nombre que le quieres dar al `.ipa` resultante.

También puedes lanzarlo desde la web: pestaña **Actions** de tu copia de este repo →
**Build unsigned iOS IPA** → **Run workflow**, y rellenas los mismos campos.

Cuando termine (tarda 10-15 min), baja a la sección **Artifacts** de esa ejecución y
descarga el `.ipa`. Caduca a los 14 días (límite de GitHub Actions).

## Notas

- El `.ipa` no está firmado por Apple: eso es intencional, Sideloadly/AltStore lo
  firman ellos con tu Apple ID gratis al instalarlo.
- Con firma gratuita de Apple, la app deja de abrirse a los 7 días salvo que la
  refresques con Sideloadly/AltServer.
- Consume minutos de runner macOS de tu cuenta de GitHub Actions (los runners macOS
  cuentan 10x en la cuota gratuita).
- Si el token no tiene acceso al repo objetivo, el primer paso (`Checkout target
  project`) fallará con un error de permisos.

## Licencia

MIT — úsalo, modifícalo y compártelo libremente. Ver [LICENSE](LICENSE).
