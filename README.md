# IndViSi

**Automated processing of CNBV / SIARA regulatory requests for Mexican financial institutions.**
*Procesamiento automatizado de requerimientos de autoridad (SIARA) para instituciones financieras.*

Banks, brokers and SOFOMs in Mexico receive a steady stream of *oficios* from authorities through
the CNBV's SIARA portal: freezes (*aseguramientos*), unfreezes, information requests and fund
transfers, each with a legal deadline. Each case can arrive as several companion files (PDF, DOCX,
XML) that someone has to read, reconcile, classify and answer.

IndViSi automates that work: it ingests the case, reads each document (native text or OCR),
fuses the sources, classifies the legal directive, tracks deadlines and exports the response.

Project and namespace names still use the original `ExxerCube.Prisma` name.

## Components

- **Orion**: ingestion workers.
- **Athena**: document processing workers.
- **Sentinel**: monitoring.
- **Extraction and imaging**: Tesseract OCR, native PDF text (PdfPig), image analysis and filters (Emgu CV).
- **Classification and export**: legal directive classification, SLA tracking, SIRO XML and adaptive export.
- **Web UI**: Blazor Server and MudBlazor.
- **Siara.Simulator**: stand-alone SIARA portal simulator for tests and demos.

## Tech

.NET 10 · C# with hexagonal architecture and `Result<T>` error handling · Blazor Server · EF Core
and SQL Server · Tesseract · Emgu CV · PdfPig · Playwright · SignalR · optional Python models
through CSnakes · xUnit v3, Shouldly, NSubstitute and Testcontainers · OpenTelemetry.

## Getting started

Requires the .NET 10 SDK.

```bash
cd Prisma/Code/Src/CSharp
dotnet build ExxerCube.Prisma.sln
dotnet test ExxerCube.Prisma.sln --no-build
```

Build output goes to `Prisma/Code/Src/CSharp/artifacts/`. Set the `ArtifactsBaseDir` environment
variable to build somewhere else.

Some test suites need extra tools:

| Suites | Needs |
|---|---|
| Tesseract OCR | Tesseract and Leptonica native libraries, with `TESSDATA_PREFIX` pointing to the `spa` and `eng` language data |
| OCR pipeline | Emgu CV native libraries |
| GOT-OCR2 and Python filters | The Python environment from `Prisma/Samples/GotOcr2Sample/PythonOcrLib/requirements.txt` |
| UI and end-to-end | Playwright browsers |
| Database integration | Docker |

## Status

Development snapshot, not ready for production. Four architecture tests currently report known
design issues, and the `GotOcr2Sample` solution does not build yet.

## Repository layout

```
Prisma/Code/Src/CSharp/   .NET solution
Prisma/Code/Src/Python/   OCR pipeline and synthetic document generator
Prisma/Fixtures/          Test documents
Siara.Simulator/          SIARA portal simulator
UnifiedHub/               SignalR abstractions
Docs/                     Architecture and design documents
```

## Test data

Test documents are invented. "Banco Ejemplo", the institution used in the fixtures, does not
exist. The simulator is a test double and is not affiliated with the CNBV or the Government of
Mexico.

## License

Source-available under the [PolyForm Noncommercial License 1.0.0](LICENSE.md): free for study,
research and evaluation. **Commercial use requires a commercial license**; see
[`COMMERCIAL-LICENSE.md`](COMMERCIAL-LICENSE.md).

## Contact

Built by ExxerCube / IndFusion. To ask about a pilot or a commercial license, open an issue or
reach out through GitHub.
