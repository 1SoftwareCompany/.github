<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/banner.svg" alt="1Software — event-driven .NET and developer tooling" width="100%">
</p>

<p align="center">
  <a href="https://www.nuget.org/packages/1nception"><img src="https://img.shields.io/nuget/v/1nception?label=1nception&logo=nuget&color=7c3aed" alt="1nception on NuGet"></a>
  <a href="https://www.nuget.org/packages/1nception"><img src="https://img.shields.io/nuget/dt/1nception?label=downloads&logo=nuget&color=0ea5e9" alt="1nception downloads"></a>
  <a href="https://www.nuget.org/packages/1nception.Projections.Cassandra"><img src="https://img.shields.io/nuget/v/1nception.Projections.Cassandra?label=Projections.Cassandra&logo=nuget&color=14b8a6" alt="1nception.Projections.Cassandra on NuGet"></a>
  <a href="https://www.nuget.org/packages/1nception.EventStore.Cassandra"><img src="https://img.shields.io/nuget/v/1nception.EventStore.Cassandra?label=EventStore.Cassandra&logo=nuget&color=14b8a6" alt="1nception.EventStore.Cassandra on NuGet"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/C%23-.NET-512BD4?logo=dotnet&logoColor=white" alt=".NET">
  <img src="https://img.shields.io/badge/Cassandra-1287B1?logo=apachecassandra&logoColor=white" alt="Cassandra">
  <img src="https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white" alt="RabbitMQ">
  <img src="https://img.shields.io/badge/Consul-F24C53?logo=consul&logoColor=white" alt="Consul">
  <img src="https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white" alt="Redis">
</p>

---

## 🌀 1nception: event sourcing for .NET, with the hard parts already done

Most event-sourcing libraries stop at *"store the events"*. 1nception also takes care of everything that comes after:
rebuilding read models when the code changes, reading the past, replaying history, isolating tenants, and keeping it all running in production.

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/features.svg" alt="Versioned projections, time-travel reads, replay, multitenancy, stream integrity, migrations, ops API, auto-updates" width="100%">
</p>

---

## ✨ Projections that rebuild themselves

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/projection-versioning.svg" alt="A changed projection is rebuilt from the event store in the background while the live version keeps serving reads" width="100%">
</p>

A projection is identified by the events it handles. Add or remove an `IEventHandle<T>` and its hash changes, so 1nception
**starts a new version**, replays the event store into it and **switches reads over once it has caught up**. No migration scripts, no downtime, no "please wait while we rebuild".

- The rebuild runs as a cluster job with **progress signals**, a **timebox**, and can be **paused, cancelled or finalized** through the API.
- Projections are event-sourced with **snapshots**, so loading stays fast however long the history gets.
- Need to **fix** a broken read model? Trigger a rebuild of the same version through the API.

---

## ⏪ Time-travel queries

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/time-travel.svg" alt="Read a projection exactly as it was at any point in time" width="100%">
</p>

Because projections are built from events, the past is never lost. Ask for any projection **as it was at any moment**:
audits, support tickets ("what did the customer see on Tuesday?"), reports for a past period, debugging a production issue.

---

## 🧩 Built for production

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/production.svg" alt="Multitenancy, replay, event stream integrity, ops API, event store migrations and auto-updates" width="100%">
</p>

---

## 🏗️ Architecture

Pick the core, then plug in the storage, transport and clustering you need.

```mermaid
flowchart LR
    subgraph svc [Your service]
        direction TB
        API["Api · AspNet"] --> CORE(("1nception"))
        DOMAIN["Domain"] --> CORE
    end
    subgraph store [Storage]
        direction TB
        ES[("EventStore.Cassandra")]
        PR[("Projections.Cassandra")]
        AA["AtomicAction.Redis"]
    end
    subgraph infra [Infrastructure]
        direction TB
        MQ{{"Transport.RabbitMQ"}}
        CL["Cluster.Consul"]
        SER["Serialization.NewtonsoftJson"]
        MT["One.MessageTracing"]
    end
    CORE == "events · read models · locks" ==> store
    CORE == "messages · discovery · tracing" ==> infra
    classDef core fill:#1e1b4b,stroke:#a78bfa,stroke-width:2px,color:#f8fafc
    classDef pkg fill:#0f172a,stroke:#5eead4,color:#e0e7ff
    class CORE core
    class API,DOMAIN,ES,PR,AA,MQ,CL,SER,MT pkg
    style svc fill:#0b1222,stroke:#2a3558,color:#c7d2fe
    style store fill:#0b1222,stroke:#2a3558,color:#c7d2fe
    style infra fill:#0b1222,stroke:#2a3558,color:#c7d2fe
```

<details>
<summary><b>📦 All 1nception packages</b></summary>
<br>

| Layer | Packages |
|---|---|
| **Core** | [`1nception`](https://github.com/1SoftwareCompany/1nception) · [`Domain`](https://github.com/1SoftwareCompany/1nception.Domain) · [`Api`](https://github.com/1SoftwareCompany/1nception.Api) · [`AspNet`](https://github.com/1SoftwareCompany/1nception.AspNet) |
| **Persistence** | [`EventStore.Cassandra`](https://github.com/1SoftwareCompany/1nception.EventStore.Cassandra) · [`Projections.Cassandra`](https://github.com/1SoftwareCompany/1nception.Projections.Cassandra) · [`AtomicAction.Redis`](https://github.com/1SoftwareCompany/1nception.AtomicAction.Redis) |
| **Infrastructure** | [`Transport.RabbitMQ`](https://github.com/1SoftwareCompany/1nception.Transport.RabbitMQ) · [`Cluster.Consul`](https://github.com/1SoftwareCompany/1nception.Cluster.Consul) · [`Serialization.NewtonsoftJson`](https://github.com/1SoftwareCompany/1nception.Serialization.NewtonsoftJson) |
| **Tooling** | [`SourceGeneration.AutoUpdater`](https://github.com/1SoftwareCompany/1nception.SourceGeneration.AutoUpdater) |

</details>

```bash
dotnet add package 1nception
```

📖 **[Read the docs](https://github.com/1SoftwareCompany/1nception/tree/master/docs)** · 🚀 **[Quick start](https://github.com/1SoftwareCompany/1nception/tree/master/docs/getting-started/quick-start)**

---

## 🔎 Around the framework

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/tracing.svg" alt="One.MessageTracing links every message by correlation and causation IDs" width="100%">
</p>

<table>
<tr>
<td width="50%" valign="top">

**[One.MessageTracing](https://github.com/1SoftwareCompany/One.MessageTracing)**<br>
Records the message, correlation and causation ID of every action, so you can follow any chain of events from start to finish.

</td>
<td width="50%" valign="top">

**[Sett1x](https://github.com/1SoftwareCompany/Sett1x)**<br>
Externalized configuration per cluster and machine, kept outside your repo.
Extensions: [Consul](https://github.com/1SoftwareCompany/Sett1x.Consul) · [RabbitMQ](https://github.com/1SoftwareCompany/Sett1x.RabbitMQ) · [CLI](https://github.com/1SoftwareCompany/Sett1x.Cli)

</td>
</tr>
</table>

---

<p align="center">
  <sub>Found a bug or have an idea? Open an issue in the relevant repository. Contributions are welcome, and ⭐ stars help others find 1nception.</sub>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/1SoftwareCompany/.github/main/profile/footer.svg" alt="Built in the open by 1Software" width="100%">
</p>
