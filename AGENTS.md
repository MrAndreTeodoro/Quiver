# AGENTS.md — Quiver

Quiver is a local Rails application that ingests job offers, scores fit against a professional profile, and generates tailored CVs and cover letters in Markdown, DOCX, and PDF.

This file is the entry point for any AI coding agent. Read all PRD files in docs/prd/ before implementing anything.

---

## Tech Stack

- Rails 8.1.2, SQLite3, TailwindCSS, Importmap
- ruby_llm gem (AI model integration)
- Faraday + Nokogiri (HTTP + HTML parsing)
- Caracal gem (DOCX export)
- Prawn + prawn-table (PDF export)
- Redcarpet (Markdown rendering in views)
- ActiveStorage + mini_magick (image uploads)

---

## Bootstrap Commands

```bash
rails new quiver --database=sqlite3 --css=tailwind --javascript=importmap --skip-test --skip-action-mailer --skip-action-mailbox --skip-action-cable --skip-active-storage
cd quiver
```

Add to Gemfile:

```ruby
gem "ruby_llm"
gem "faraday"
gem "nokogiri"
gem "caracal"
gem "prawn"
gem "prawn-table"
gem "redcarpet"
gem "image_processing"
gem "mini_magick"
gem "dotenv-rails", groups: [:development, :test]
```

```bash
bundle install
rails active_storage:install
rails db:migrate
```

---

## Environment Variables

Create a `.env` file in the project root:

```
ANTHROPIC_API_KEY=your_key_here
```

Create `config/initializers/ruby_llm.rb`:

```ruby
RubyLLM.configure do |config|
  config.anthropic_api_key = ENV.fetch("ANTHROPIC_API_KEY")
end
```

---

## Implementation Order

Implement in this order to avoid dependency issues:

1. Models and migrations (PRD_MODELS.md)
2. Service objects (PRD_SERVICES.md)
3. Export services (PRD_EXPORTS.md)
4. Routes and controllers (PRD_CONTROLLERS.md)
5. Views and Stimulus controller (PRD_VIEWS.md)
6. Seed profile (PRD_PROFILE.md)

---

## AI Models

- `claude-haiku-4-5-20251001` — for parsing tasks (fast, cheap, structured extraction)
- `claude-sonnet-4-20250514` — for scoring and generation (higher quality output needed)

---

## Key Business Rules

- `FIT_THRESHOLD = 65.0` defined as a constant on the JobOffer model
- Document generation is only allowed if `fit_score >= FIT_THRESHOLD`
- Profile is always a single record — accessed via `Profile.instance`
- AI prompts must explicitly instruct the model to never invent experience, metrics, or skills
- All exports saved to `Rails.root/storage/exports/`
- Application status starts as `"not_applied"` and is updated manually by the user

---

## PRD Index

| File | Contents |
|---|---|
| PRD.md | Overview, user flow, constraints |
| PRD_MODELS.md | Schema, model code, migrations |
| PRD_SERVICES.md | Service objects with full specs |
| PRD_EXPORTS.md | DOCX and PDF export implementation |
| PRD_CONTROLLERS.md | Routes and controller specs |
| PRD_VIEWS.md | UI layout, views, Stimulus controller |
| PRD_PROFILE.md | Profile format and seed strategy |
