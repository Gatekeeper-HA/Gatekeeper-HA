# Contributing to Gatekeeper-HA

Thanks for helping. This repository is the Home Assistant add-on: its packaging (`ha-addon/`),
options and documentation. The code it runs is the `gatekeeper` package in the
[Gatekeeper](https://github.com/Gatekeeper-HA/Gatekeeper) repository; bugs in visitor
handling, speech or notifications belong there.

- **Bugs and ideas:** open an issue. Include the add-on's log, your Home Assistant and
  Frigate versions, and your camera.
- **Larger changes:** open an issue first to agree on the approach before you write the
  code.

## Contributor License Agreement

Before we can merge your first pull request, you need to agree to the
[Contributor License Agreement](https://github.com/Gatekeeper-HA/Gatekeeper/blob/main/CLA.md).
A single agreement covers every repository in the Gatekeeper-HA organization. A bot
comments on the pull request with instructions; agreeing takes one comment.

In short:
- You keep the copyright in your work.
- You give Lobo Dorado LLC, which maintains Gatekeeper, a broad license to it. That lets the
  project also be offered under other terms, such as a commercial license for companies that
  build Gatekeeper into their products.
- We promise that any version including your work is also available under an open-source
  license.

The CLA itself is what counts.

**AI-assisted contributions are welcome.** Review what the tool wrote as if it were your own,
and say in the pull request which parts substantially came from an AI tool.

## Making changes

- **Options** live in three places, and a change needs all three:
  - `ha-addon/config.yaml` (`options` and `schema`);
  - `ha-addon/translations/en.yaml`;
  - the `run` script that exports them (`ha-addon/rootfs/etc/s6-overlay/s6-rc.d/gatekeeper/run`).

  Document each option in `ha-addon/DOCS.md`.
- **The Gatekeeper version** the add-on installs is `GATEKEEPER_REF` in `ha-addon/build.yaml`.
- **To try a change** on Home Assistant OS, copy the `ha-addon` folder into the `/addons`
  share (e.g. with the Samba or SSH add-on). It then appears under *Local add-ons* in the
  store.

## Pull requests

- Branch from `main` and open a pull request against it. `main` is protected: CI (the Home
  Assistant app linter and ShellCheck on the `run` script) must pass before merging.
- Add a line under *Unreleased* in [CHANGELOG.md](CHANGELOG.md).
- Keep each pull request to one change.
