# 🤝 Guía de contribución

Gracias por contribuir. Este documento explica **cómo trabajamos**: ramas, Pull Requests (PR) y revisión de código (code review).

> **Glosario rápido**
> - **Branch (rama):** una línea paralela de trabajo para no tocar `main` directamente.
> - **Pull Request (PR):** solicitud para unir tus cambios a otra rama, con espacio para revisión.
> - **Code review:** que otra persona lea tu código antes de aceptarlo.
> - **CI (Integración Continua):** robot que corre pruebas y validaciones en cada PR.

---

## 1. Preparar tu entorno

```bash
git clone https://github.com/<tu-org>/<repo>.git
cd <repo>
git checkout -b feat/mi-cambio      # siempre trabaja en una rama nueva
```

Cada proyecto debe tener en su README los comandos para instalar, correr y probar. Si falta algo, abre un issue.

---

## 2. Estrategia de ramas

Usamos un flujo **simple, basado en trunk** (una sola rama principal y ramas cortas):

| Rama | Uso | Reglas |
|---|---|---|
| `main` | Siempre estable y desplegable. | **Protegida**: nadie hace push directo. |
| `feat/*`, `fix/*`, `docs/*`, `chore/*`, `refactor/*`, `test/*` | Trabajo del día a día. | Vida corta (idealmente < 3 días). Se borra al hacer merge. |
| `hotfix/*` | Corrección urgente en producción. | Sale de `main`, PR rápido, se despliega de inmediato. |
| `release/*` | *Solo si* el proyecto necesita mantener varias versiones. | Opcional. |

### Nombres de rama

Formato: `tipo/descripcion-corta-en-minusculas`, con guiones y, si hay issue, su número.

```text
✅ feat/login-con-google
✅ fix/123-error-al-guardar-perfil
✅ docs/actualizar-readme
❌ mi-rama
❌ Fix_Bug_Importante
❌ alan-pruebas
```

### Configuración recomendada de `main` (GitHub → Settings → Branches)

- [x] Require a pull request before merging
- [x] Require approvals: **1** (2 si toca seguridad o infraestructura)
- [x] Dismiss stale approvals when new commits are pushed
- [x] Require status checks to pass (CI: lint + tests + build)
- [x] Require conversation resolution before merging
- [x] Require linear history (usamos *squash merge*)
- [x] Block force pushes

---

## 3. Flujo de trabajo paso a paso

1. **Crea o elige un issue** que describa el cambio (si es muy pequeño, puede omitirse).
2. **Crea tu rama** desde `main` actualizado:
   ```bash
   git checkout main && git pull
   git checkout -b fix/123-error-al-guardar-perfil
   ```
3. **Haz commits pequeños** siguiendo [`COMMITS.md`](COMMITS.md).
4. **Corre localmente** formato, lint y pruebas antes de subir.
5. **Sube tu rama** y abre un **PR** con la plantilla completa.
6. **Atiende los comentarios** de la revisión con nuevos commits (no reescribas historia durante la revisión).
7. **Merge** con *Squash and merge* cuando haya aprobación y CI en verde.
8. **Borra la rama** (GitHub lo ofrece automáticamente).

---

## 4. Reglas para Pull Requests

- **Pequeños:** apunta a **menos de ~400 líneas cambiadas**. Si es más grande, divídelo.
- **Un propósito por PR:** no mezcles un bugfix con un refactor y una feature.
- **Título en formato Conventional Commits** (porque será el mensaje del squash):
  `feat(auth): agrega inicio de sesión con Google`
- **Enlaza el issue:** `Closes #123` en la descripción.
- **Incluye pruebas** para código nuevo o bugs corregidos.
- **Incluye evidencia** si cambia la UI (captura o video).
- **Usa PR en borrador (Draft)** si pides feedback temprano.
- **CI en verde** antes de pedir revisión.
- **Nunca** subas secretos, tokens ni archivos `.env` (ver sección 7).

### Definición de "terminado" (Definition of Done)

- [ ] Cumple lo pedido en el issue
- [ ] Pruebas nuevas o actualizadas pasan
- [ ] Lint y formato sin errores
- [ ] Sin secretos ni datos personales en código o logs
- [ ] Documentación actualizada (README, comentarios públicos, CHANGELOG si aplica)
- [ ] Revisado y aprobado por al menos una persona

---

## 5. Code review

### Para quien **revisa**

- **Responde en máximo 1 día hábil.** Una revisión lenta bloquea al equipo.
- **Revisa en este orden:** (1) ¿resuelve el problema? (2) seguridad (3) diseño y claridad (4) pruebas (5) estilo.
- **Critica el código, no a la persona.** Pregunta antes de asumir: *"¿Qué pasa si `user` es nulo aquí?"*
- **Explica el porqué** de tus sugerencias, y ofrece alternativas.
- **Usa prefijos** para dejar clara la importancia:

| Prefijo | Significado |
|---|---|
| `bloqueante:` | Debe corregirse antes del merge. |
| `sugerencia:` | Mejora recomendada, no obligatoria. |
| `duda:` | Pregunta genuina; no pide cambio. |
| `nit:` | Detalle menor (estilo, ortografía). |
| `bien:` | Reconocimiento. ¡Úsalo! 🎉 |

- **No pidas lo que una herramienta puede automatizar** (formato, imports): configura el linter.

### Para quien **es revisado**

- **Revisa tu propio PR primero** (pestaña *Files changed*) antes de pedir revisión.
- **Explica el contexto** en la descripción: qué, por qué y cómo probarlo.
- **No te lo tomes personal:** la revisión protege el producto, no evalúa tu valor.
- **Responde cada comentario**: aplica el cambio o explica por qué no.
- **Marca la conversación como resuelta** solo cuando quien comentó esté de acuerdo.

### Checklist del revisor (resumen)

- [ ] ¿Hace lo que dice el issue y nada más?
- [ ] ¿Valida entradas y maneja errores?
- [ ] ¿Hay riesgos de seguridad (inyección, permisos, secretos, datos personales)?
- [ ] ¿Los nombres y la estructura son claros?
- [ ] ¿Las pruebas cubren casos normales **y** casos límite?
- [ ] ¿Cambia contratos públicos (API, base de datos) sin migración ni aviso?

---

## 6. Reportar bugs y proponer mejoras

Usa las plantillas en **Issues → New issue**:

- 🐞 **Bug report:** algo no funciona como debería.
- ✨ **Feature request:** una mejora o funcionalidad nueva.

Antes de abrir uno, **busca si ya existe**.

---

## 7. Seguridad

- **Nunca** hagas commit de contraseñas, tokens, llaves privadas ni archivos `.env`. Usa `.env.example` con valores falsos.
- Si subiste un secreto por error: **considéralo comprometido**. Revócalo/rótalo de inmediato; borrar el commit **no** basta.
- Vulnerabilidades: **no abras un issue público**. Repórtalas de forma privada (GitHub → Security → *Report a vulnerability*) o al correo definido en `SECURITY.md` del proyecto.
- Habilita en cada repo: **Dependabot**, **secret scanning** y **code scanning** (CodeQL).

---

## 8. Código de conducta

Trata a todas las personas con respeto. No toleramos acoso, discriminación ni ataques personales. Las discusiones técnicas se resuelven con argumentos y datos.
