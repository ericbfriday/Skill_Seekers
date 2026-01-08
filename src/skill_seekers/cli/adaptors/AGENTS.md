# PLATFORM ADAPTORS

Strategy Pattern for multi-LLM platform support. Each platform has unique packaging/upload requirements.

## STRUCTURE

```
adaptors/
├── __init__.py    # Registry + factory (get_adaptor, list_platforms)
├── base.py        # SkillAdaptor ABC + SkillMetadata dataclass
├── claude.py      # Claude AI (ZIP + YAML frontmatter)
├── gemini.py      # Google Gemini (tar.gz)
├── openai.py      # OpenAI ChatGPT (ZIP + Vector Store)
└── markdown.py    # Generic Markdown (universal export)
```

## ADDING NEW PLATFORM

1. Create `{platform}.py` inheriting `SkillAdaptor`
2. Implement required methods:
   - `format_skill_md()` - platform-specific SKILL.md
   - `package()` - ZIP/tar.gz creation
   - `upload()` - API integration
3. Register in `__init__.py` ADAPTORS dict
4. Add optional deps to `pyproject.toml [project.optional-dependencies]`

## API CONTRACT

```python
class SkillAdaptor(ABC):
    PLATFORM: str          # "claude", "gemini", etc
    PLATFORM_NAME: str     # Human-readable name
    
    @abstractmethod
    def format_skill_md(skill_dir, metadata) -> str
    
    @abstractmethod
    def package(skill_dir, output_path) -> Path
    
    @abstractmethod
    def upload(package_path, api_key, **kwargs) -> Dict
    
    def validate_api_key(api_key) -> bool      # Optional override
    def get_env_var_name() -> str              # e.g., "ANTHROPIC_API_KEY"
    def supports_enhancement() -> bool
    def enhance(skill_dir, api_key) -> bool
```

## PLATFORM SPECIFICS

| Platform | Package | Frontmatter | API Key Env |
|----------|---------|-------------|-------------|
| Claude | .zip | YAML | ANTHROPIC_API_KEY |
| Gemini | .tar.gz | None | GOOGLE_API_KEY |
| OpenAI | .zip + Vector Store | None | OPENAI_API_KEY |
| Markdown | .zip | None | None (manual) |
