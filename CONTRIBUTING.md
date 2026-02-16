# Contributing to Building Materials Price Intelligence Platform

Thank you for your interest in contributing to the Building Materials Price Intelligence Platform! This document provides guidelines for contributing to this project.

## 📋 Current Project Status

**This is an Academic Project**

This project is currently being developed as part of the **Informatics 3** program at the **Academy of Computer Science & Software Engineering** (March 9 - June 9, 2026).

### During Development Phase (Current)

**Team Contributions Only:**
- Contributions are currently **limited to the 4-member project team**
- External contributions are **not accepted** during the academic evaluation period
- This ensures academic integrity and fair evaluation

**Why This Restriction?**
- Academic requirements mandate original work by team members
- External contributions could compromise project evaluation
- Ensures all team members receive appropriate credit

---

## 🚀 Post-Academic Phase (After June 2026)

After project completion and evaluation, we plan to:
1. ✅ Select and apply an open source license
2. ✅ Open the project for community contributions
3. ✅ Establish contribution guidelines
4. ✅ Set up issue tracking and project boards
5. ✅ Welcome external contributors!

**Expected Timeline:**
- **June 10, 2026**: Final project presentation
- **July 1, 2026**: Open source license applied
- **July 15, 2026**: Community contributions enabled

---

## 👀 How You Can Help Now

While direct code contributions aren't accepted during development, you can:

### 1. Star and Watch
- ⭐ **Star** the repository to show support
- 👁️ **Watch** for updates and releases
- 🔔 Get notified when contributions are enabled

### 2. Provide Feedback
- Report bugs you discover (we'll evaluate and fix)
- Suggest features for post-MVP development
- Share the project with others in construction/tech

### 3. Spread the Word
- Share with construction industry professionals
- Discuss in relevant communities (construction tech, ML, South African startups)
- Write about the project (with proper attribution)

### 4. Prepare for Future Contributions
- Review the codebase to understand architecture
- Familiarize yourself with the tech stack
- Identify areas where you'd like to contribute
- Check back after July 2026!

---

## 🎓 For Academic Peers

### Academic Integrity Policy

**DO NOT:**
- ❌ Copy code for your own academic submissions (plagiarism)
- ❌ Submit similar projects without proper differentiation
- ❌ Claim authorship or contribution without permission

**YOU MAY:**
- ✅ Reference the project in your research (with citation)
- ✅ Learn from the architecture and approach
- ✅ Use as inspiration for your own original projects
- ✅ Discuss technical approaches in academic contexts

### Proper Citation

If referencing this project academically:

```
Building Materials Price Intelligence Platform Team (2026).
Building Materials Price Intelligence Platform: A Machine Learning-Driven
System for Price Forecasting and Supplier Comparison.
Informatics 3 Project, Academy of Computer Science & Software Engineering.
GitHub: https://github.com/imperfectperson-max/build_material
```

---

## 🔮 Future Contribution Guidelines (Coming July 2026)

Once the project opens for contributions, we'll welcome:

### Types of Contributions

**Code Contributions:**
- 🐛 Bug fixes
- ✨ New features
- 🎨 UI/UX improvements
- ⚡ Performance optimizations
- 📝 Documentation improvements
- 🧪 Test coverage enhancements

**Non-Code Contributions:**
- 📊 Dataset expansions (additional suppliers, regions)
- 📚 Documentation and tutorials
- 🌍 Translations (internationalization)
- 🎨 Design assets (logos, icons, graphics)
- 💡 Feature proposals and specifications

### Contribution Areas

| Component | Tech Stack | Contribution Opportunities |
|-----------|-----------|---------------------------|
| **Backend** | Node.js, PostgreSQL, Redis | API endpoints, caching strategies, security |
| **ML Models** | Python, XGBoost, LSTM, Prophet | Model improvements, new algorithms, feature engineering |
| **Web Frontend** | React, Tailwind CSS, Recharts | UI components, data visualization, accessibility |
| **Mobile App** | Flutter, Hive | Cross-platform features, offline functionality, UX |
| **Data Collection** | Web scraping, Python | New supplier integrations, data quality |
| **DevOps** | CI/CD, Docker, Cloud platforms | Deployment automation, monitoring, scaling |

---

## 📋 Planned Contribution Workflow (Post-July 2026)

### Step 1: Find or Create an Issue
- Browse existing issues for good first issues
- Create new issue describing bug/feature
- Discuss approach before starting work

### Step 2: Fork and Branch
```bash
# Fork the repository on GitHub
git clone https://github.com/YOUR_USERNAME/build_material.git
cd build_material

# Create feature branch
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bug-fix
```

### Step 3: Make Changes
- Follow existing code style and conventions
- Write clear, descriptive commit messages
- Add tests for new functionality
- Update documentation as needed

### Step 4: Test Locally
```bash
# Run tests
npm test           # Backend tests
pytest            # ML tests
npm run test:web  # Frontend tests
flutter test      # Mobile tests

# Run linters
npm run lint
flake8            # Python linting
```

### Step 5: Submit Pull Request
- Push your branch to your fork
- Open PR against `main` branch
- Provide clear description of changes
- Link related issues
- Wait for code review

### Step 6: Code Review
- Respond to reviewer feedback
- Make requested changes
- Maintain respectful communication
- Iterate until approved

### Step 7: Merge
- PR will be merged by maintainers
- Your contribution will be recognized!
- You'll be added to contributors list

---

## 💻 Development Setup (For Future Contributors)

### Prerequisites
- Node.js v18+
- Python 3.9+
- PostgreSQL 14+
- Redis 6+
- Flutter SDK 3+

### Local Development
```bash
# Clone repository
git clone https://github.com/imperfectperson-max/build_material.git
cd build_material

# Backend setup
cd backend
npm install
cp .env.example .env
# Configure .env with your credentials
npm run dev

# Web frontend setup
cd ../web
npm install
cp .env.example .env
npm run dev

# Mobile app setup
cd ../mobile
flutter pub get
flutter run

# ML development setup
cd ../ml
pip install -r requirements.txt
jupyter notebook
```

---

## 📝 Code Style Guidelines

### General Principles
- **Clarity over cleverness**: Write readable code
- **Consistency**: Follow existing patterns
- **Documentation**: Comment complex logic
- **Testing**: Write tests for new features

### Language-Specific

**JavaScript/TypeScript:**
- ESLint with Airbnb config
- Prettier for formatting
- 2-space indentation
- Use async/await over promises

**Python:**
- PEP 8 style guide
- Black formatter
- Type hints encouraged
- Docstrings for functions/classes

**Flutter/Dart:**
- Follow Effective Dart guidelines
- Use Flutter linter rules
- Widget composition over inheritance

---

## 🧪 Testing Requirements

**All contributions must include tests:**

- **Unit Tests**: For individual functions/components
- **Integration Tests**: For API endpoints, data pipelines
- **End-to-End Tests**: For critical user flows
- **ML Model Tests**: For prediction accuracy, data validation

**Test Coverage Target:** 80%+ for new code

---

## 📖 Documentation Standards

**Update documentation for:**
- New features or API changes
- Configuration changes
- Breaking changes
- Complex algorithms or business logic

**Documentation Locations:**
- Code comments: Inline explanations
- README.md: High-level project info
- TECHNOLOGY_CHOICES.md: Technical decisions
- SECURITY.md: Security considerations
- API docs: OpenAPI/Swagger specifications

---

## 🤝 Community Guidelines

### Be Respectful
- Treat all contributors with respect
- Constructive criticism only
- No discrimination or harassment
- Follow Code of Conduct

### Communication Channels (Future)
- **GitHub Issues**: Bug reports, feature requests
- **GitHub Discussions**: Questions, ideas, general discussion
- **Pull Requests**: Code review and technical discussion
- **Discord/Slack** (TBD): Real-time communication

### Response Times
- We aim to respond to issues/PRs within 3 business days
- Complex PRs may take longer to review
- Be patient and respectful of maintainer time

---

## 🏆 Recognition

Contributors will be recognized in:
- **CONTRIBUTORS.md**: List of all contributors
- **GitHub Contributors Page**: Automatic recognition
- **Release Notes**: Credited for specific features
- **Project Website** (future): Hall of fame

---

## 🎯 Priorities for Future Contributions

**High Priority (Post-MVP):**
- [ ] Additional South African supplier integrations
- [ ] API performance optimizations
- [ ] Mobile app polish and bug fixes
- [ ] ML model accuracy improvements
- [ ] Security hardening and penetration testing

**Medium Priority:**
- [ ] WhatsApp/SMS alert integration
- [ ] Advanced data visualization features
- [ ] Project management system integrations
- [ ] Multi-language support (Afrikaans, Zulu, Xhosa)
- [ ] API rate limit improvements

**Lower Priority (Phase 2):**
- [ ] Expansion to other African countries
- [ ] Reinforcement learning for purchase timing
- [ ] Supplier API integrations (when available)
- [ ] Advanced anomaly detection
- [ ] Macroeconomic indicator integration

---

## ❓ Questions?

**During Development (Now - June 2026):**
- Not accepting external contributions
- Questions can be asked via GitHub Issues
- We'll respond when possible between project work

**After Launch (July 2026+):**
- Check GitHub Discussions first
- Review existing issues
- Join community chat channels
- Contact maintainers if needed

---

## 📅 Timeline Recap

| Date | Milestone | Contribution Status |
|------|-----------|-------------------|
| **Mar 9, 2026** | Project Start | ❌ Team only |
| **June 9, 2026** | Project End | ❌ Team only |
| **June 10, 2026** | Final Presentation | ❌ Team only |
| **July 1, 2026** | License Applied | 🟡 Preparing |
| **July 15, 2026** | **Contributions Open!** | ✅ Community welcome |

---

## 🙏 Thank You

We appreciate your interest in this project! While we can't accept contributions yet, we look forward to collaborating with the community after academic evaluation.

**See you in July 2026! 🚀**

---

**Last Updated:** March 9, 2026  
**Status:** Development Phase - Team Only  
**Contributions Open:** July 15, 2026 (Expected)  
**Questions?** Open a GitHub Issue
