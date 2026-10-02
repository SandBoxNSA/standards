# 📝 Convención de commits

Usamos **[Conventional Commits 1.0.0](https://www.conventionalcommits.org/es/v1.0.0/)** con mensajes **en español**.

> **¿Por qué?** Un historial ordenado permite leer qué pasó, generar `CHANGELOG` automáticamente y decidir la siguiente versión ([SemVer](https://semver.org/lang/es/)) sin adivinar.

---

## 1. Formato

```text
<tipo>(<alcance opcional>): <descripción corta>

[cuerpo opcional: qué y por qué]

[pie opcional: referencias, BREAKING CHANGE]
```

### Reglas de la primera línea

- **Máximo 72 caracteres.**
- **Tipo en minúsculas**, seguido de `:` y un espacio.
- **Verbo en presente/imperativo** que describa lo que el commit *hace*: `agrega`, `corrige`, `elimina`.
- **Sin punto final.**
- **Alcance** (entre paréntesis) = parte afectada: `auth`, `api`, `ui`, `db`, `deps`.

---

## 2. Tipos

| Tipo | Úsalo para... | Efecto en versión (SemVer) |
|---|---|---|
| `feat` | Nueva funcionalidad para el usuario. | **MINOR** |
| `fix` | Corrección de un bug. | **PATCH** |
| `docs` | Solo documentación. | — |
| `style` | Formato (espacios, comas). **No** cambia lógica. | — |
| `refactor` | Reorganizar código sin cambiar su comportamiento. | — |
| `perf` | Mejora de rendimiento. | PATCH |
| `test` | Agregar o corregir pruebas. | — |
| `build` | Sistema de build o dependencias (npm, pip, Docker). | — |
| `ci` | Configuración de CI/CD (GitHub Actions). | — |
| `chore` | Tareas de mantenimiento que no tocan `src` ni tests. | — |
| `revert` | Revierte un commit anterior. | Depende |
| `security` *(opcional del equipo)* | Correcciones de seguridad. | PATCH |

> Un cambio **incompatible** (BREAKING CHANGE) sube la versión **MAJOR**, sin importar el tipo.

---

## 3. Ejemplos en español

### ✅ Commits simples

```text
feat(auth): agrega inicio de sesión con Google
fix(carrito): corrige el cálculo del total con descuentos
docs(readme): agrega instrucciones de instalación
style(ui): aplica formato con Prettier
refactor(usuarios): extrae la validación de correo a una función
perf(api): agrega caché a la consulta de productos
test(pagos): agrega pruebas para tarjetas rechazadas
build(deps): actualiza express a la última versión menor
ci: agrega ejecución de pruebas en pull requests
chore: actualiza .gitignore para archivos de VS Code
revert: revierte "feat(auth): agrega inicio de sesión con Google"
```

### ✅ Con cuerpo explicativo

```text
fix(pagos): evita cobros duplicados al reintentar

Cuando el proveedor respondía con timeout, el cliente reenviaba la
solicitud sin una clave de idempotencia y se generaba un segundo cobro.
Ahora se envía un `Idempotency-Key` único por orden.

Closes #482
```

### ✅ Con cambio incompatible (BREAKING CHANGE)

```text
feat(api)!: cambia el formato de respuesta de /usuarios

BREAKING CHANGE: el campo `nombre` se divide en `nombres` y `apellidos`.
Los clientes deben actualizar su manera de leer la respuesta.
```

> El signo `!` después del tipo/alcance **o** el pie `BREAKING CHANGE:` indican el cambio incompatible.

### ✅ Referenciar issues

```text
fix(login): corrige error al iniciar sesión con correo en mayúsculas

Closes #123
Refs #120
```

`Closes #123` cierra el issue automáticamente al hacer merge.

---

## 4. Malos ejemplos y cómo corregirlos

| ❌ Mal | Por qué está mal | ✅ Mejor |
|---|---|---|
| `arreglos` | No dice qué ni dónde. | `fix(perfil): corrige error al guardar la foto` |
| `fix: cosas` | Vago. | `fix(api): valida que el id sea numérico` |
| `Feat: Agrega Login.` | Tipo en mayúscula, punto final. | `feat(auth): agrega pantalla de login` |
| `se agregó el botón de pagar y se arregló el footer y se actualizó react` | Mezcla 3 cambios. | 3 commits separados |
| `WIP` | No describe nada. | Usa un PR en borrador y commits reales. |
| `fix: corrige bug` | No hay contexto. | `fix(carrito): evita cantidades negativas` |
| `update` | Cualquier cosa. | Di **qué** se actualizó. |

---

## 5. Buenas prácticas

1. **Commits atómicos:** un commit = un cambio lógico que se puede entender (y revertir) solo.
2. **Que compile y pase pruebas** en cada commit, siempre que sea posible.
3. **Commitea seguido**, pero agrupa de forma ordenada antes de abrir el PR.
4. **Usa el cuerpo para el "porqué"**; el diff ya muestra el "qué".
5. **Nunca** incluyas secretos, `.env` ni archivos generados (`node_modules`, `__pycache__`).
6. **No reescribas historia compartida** (`git push --force` está prohibido en `main`).
7. En los PRs usamos **Squash and merge**: el **título del PR** será el mensaje final, así que debe seguir esta convención.

---

## 6. Validación automática (opcional pero recomendada)

### Node.js: commitlint + Husky

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional husky
npx husky init
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

`commitlint.config.js`:

```js
export default {
  extends: ['@commitlint/config-conventional'],
};
```

### Python: commitizen

```bash
pip install commitizen
cz commit        # asistente interactivo para escribir el commit
cz check --rev-range origin/main..HEAD   # valida en CI
```

---

## 7. Hoja de referencia rápida

```text
feat      → algo nuevo
fix       → algo arreglado
docs      → solo documentación
style     → solo formato
refactor  → mejorar estructura sin cambiar comportamiento
perf      → más rápido
test      → pruebas
build     → dependencias / empaquetado
ci        → pipelines
chore     → mantenimiento
revert    → deshacer
```
