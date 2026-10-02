# Standards

Repositorio central de **estándares de ingeniería** del equipo: cómo escribimos código, cómo hacemos commits, cómo abrimos Pull Requests y cómo reportamos problemas.

> **Idea clave:** un estándar solo sirve si es fácil de encontrar, fácil de seguir y se puede automatizar. Si una regla no se puede revisar con una herramienta (linter, CI, plantilla), la mantenemos corta y clara.

---

## 📦 ¿Qué contiene este repositorio?

| Archivo | Para qué sirve | Léelo cuando... |
|---|---|---|
| [`docs/GOOD_PRACTICES.md`](docs/GOOD_PRACTICES.md) | Guía general de buenas prácticas (ciclo de vida, seguridad, calidad, DevOps). | Empiezas un proyecto nuevo o necesitas el "por qué" de una regla. |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Flujo de trabajo: ramas, Pull Requests y code review. | Vas a hacer tu primer cambio. |
| [`CODE_STYLE.md`](CODE_STYLE.md) | Estilo de código para **Node.js / TypeScript** y **Python**. | Tienes dudas de nombres, formato o estructura. |
| [`COMMITS.md`](COMMITS.md) | Convención de commits (Conventional Commits) con ejemplos en español. | Vas a escribir un mensaje de commit. |
| [`.github/PULL_REQUEST_TEMPLATE.md`](.github/PULL_REQUEST_TEMPLATE.md) | Plantilla que aparece al abrir un PR. | Abres un Pull Request. |
| [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE/) | Formularios para reportar bugs y proponer features. | Encuentras un error o tienes una idea. |
| [`.gitignore`](.gitignore) | Archivos que Git debe ignorar (Node, Python, IDEs, SO, `.env`). | Creas un proyecto nuevo. |
| [`LICENSE`](LICENSE) | Licencia del repositorio (MIT). | Quieres reutilizar este contenido. |

---

## 🚀 Cómo usarlo

### Opción A — Como plantilla para un proyecto nuevo
1. En GitHub: **Settings → General → Template repository** 
2. En el proyecto nuevo, pulsa **Use this template**.
3. Ajusta el `README.md` y borra lo que no aplique.

### Opción B — Copiar solo lo que necesitas
```bash
# Clona este repo en una carpeta temporal
git clone https://github.com/<tu-org>/standards.git /tmp/standards

# Copia las plantillas y el .gitignore a tu proyecto
cp -r /tmp/standards/.github  ./mi-proyecto/
cp /tmp/standards/.gitignore  ./mi-proyecto/
```

### Opción C — Enlazarlo desde tu proyecto
En el `README.md` de cada proyecto agrega:

```markdown
Este proyecto sigue los [estándares del equipo](https://github.com/<tu-org>/standards).
```

---

## 🧭 Principios

1. **Seguridad por diseño:** nunca subas secretos; valida entradas; mínimo privilegio.
2. **Simple primero (KISS):** la solución más simple que funcione y se entienda.
3. **Automatiza lo repetible:** formato, lint, tests y escaneo de dependencias corren en CI.
4. **Cambios pequeños y frecuentes:** PRs chicos se revisan mejor y rompen menos.
5. **Todo cambio es trazable:** commits claros, PRs enlazados a issues.

---

## 🔄 Versionado de los estándares

Este repo usa [Versionado Semántico](https://semver.org/lang/es/):

- **MAJOR** → cambio que obliga a los proyectos a modificar algo (ej. cambiar de formateador).
- **MINOR** → nueva regla o plantilla opcional.
- **PATCH** → correcciones de redacción o ejemplos.

Los cambios se anotan en [`CHANGELOG.md`](CHANGELOG.md) (créalo con tu primer release).

---

## 🤝 ¿Quieres proponer un cambio?

Lee [`CONTRIBUTING.md`](CONTRIBUTING.md). Los estándares también se discuten con Pull Requests: **proponer cambios a las reglas sigue las mismas reglas**.

## 📄 Licencia

[MIT](LICENSE).