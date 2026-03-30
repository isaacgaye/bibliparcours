# BibliParcours Frontend Reuse Architecture Specification

## 0) Analysis Scope and Constraints

This specification reverse-engineers the current BibliParcours frontend into reusable structures **without redesigning**.

Applied constraints:
- No visual redesign
- No CSS restyling proposals
- No new branding direction
- Preserve existing structure and intent
- Focus only on layout, components, content model, and admin structure

### Source availability note
At analysis time, no HTML source pages were present in the repository (only `.gitkeep`). Therefore, this specification is derived from the supplied project context and page descriptions, and is intentionally conservative.

---

## 1) Existing Page Structure (Reverse-engineered)

## 1.1 Homepage (current intent)
Top-level structure:
1. Global header + primary navigation
2. Hero slider (4–5 slides)
3. Three featured sections:
   - Se procurer une Bible
   - Frise biblique
   - Outils
4. Testimonies section (with YouTube links)
5. Footer:
   - À propos
   - Références
   - Contact

## 1.2 Resource page (current intent)
Top-level structure:
1. Header + breadcrumb or page title block
2. Repeated resource cards (image, title, description, CTA)
3. Footer

## 1.3 Timeline page (current intent)
Top-level structure:
1. Header + page heading
2. Timeline stream (chronological entries)
3. Optional filters/period segmentation (if already present)
4. Footer

---

## 2) Reusable Component Inventory

## 2.1 Global layout components
1. **SiteHeader**
   - Logo/site title
   - Primary nav links
   - Optional CTA

2. **SiteFooter**
   - Link columns:
     - À propos
     - Références
     - Contact
   - Copyright/legal line

3. **PageShell**
   - Shared page wrapper
   - Spacing/layout slots (header/main/footer)

## 2.2 Homepage components
4. **HeroSlider**
   - List of slides (4–5)
   - Slide media/background
   - Slide title/text
   - Slide CTA link
   - Controls (prev/next/dots) if already existing

5. **FeatureSectionGrid**
   - 3 fixed featured blocks:
     - Se procurer une Bible
     - Frise biblique
     - Outils
   - Each block includes title, summary, link target

6. **TestimoniesSection**
   - Section heading
   - Testimony cards/items with:
     - Person/title
     - Short excerpt
     - YouTube URL

## 2.3 Shared content components
7. **SectionHeader**
   - Reusable heading + optional intro text

8. **ContentCard**
   - Image
   - Title
   - Description
   - CTA label + URL

9. **LinkButton / CTAButton**
   - Reusable link-style call to action

## 2.4 Resource page components
10. **ResourceList**
    - Container for repeated resources

11. **ResourceCard** (specialized from ContentCard)
    - image, title, description, CTA
    - optional tags/category

## 2.5 Timeline components
12. **TimelineContainer**
    - Chronological wrapper

13. **TimelineEntry**
    - Date / period
    - Title
    - Description/body
    - Optional media/link/reference

14. **TimelineGroup** (optional, if existing)
    - Entries grouped by era/book/period

---

## 3) Page Template Definitions

## 3.1 Template A — Homepage
Purpose: landing page with orientation and key pathways.

### Regions
1. `header` → SiteHeader
2. `hero` → HeroSlider
3. `featured` → FeatureSectionGrid (3 fixed entries)
4. `testimonies` → TestimoniesSection
5. `footer` → SiteFooter

### Slot contract
- Hero uses ordered slide list (max 5 recommended by current use)
- Featured section uses exactly 3 primary pathways
- Testimonies accepts list of video-linked items

## 3.2 Template B — Resource Page
Purpose: list page for practical resources.

### Regions
1. `header` → SiteHeader
2. `intro` → SectionHeader (page title + intro)
3. `list` → ResourceList (ResourceCard repeat)
4. `footer` → SiteFooter

### Slot contract
- Resource cards repeat from structured data
- Card fields: image, title, description, CTA
- Optional category/tag facets can be injected without altering layout

## 3.3 Template C — Timeline Page
Purpose: historical/biblical chronology view.

### Regions
1. `header` → SiteHeader
2. `intro` → SectionHeader
3. `timeline` → TimelineContainer + TimelineEntry repeat
4. `footer` → SiteFooter

### Slot contract
- Entries must be sortable by chronological key
- Support both absolute date and relative period labels
- Optional grouping (era/period) if present in current implementation

---

## 4) Data / Content Schema (Reusable)

The model below is platform-agnostic (usable with JSON, SQL tables, or headless CMS collections).

## 4.1 Global settings
**Collection: `site_settings`**
- `site_name` (string)
- `primary_nav` (array of nav items)
- `footer_about_links` (array)
- `footer_references_links` (array)
- `footer_contact_links` (array)

**Nav item object**
- `label` (string)
- `url` (string)
- `is_external` (boolean)
- `order` (number)

## 4.2 Hero slides
**Collection: `hero_slides`**
- `id` (string/uuid)
- `title` (string)
- `subtitle` (string, optional)
- `image_url` (string)
- `cta_label` (string)
- `cta_url` (string)
- `order` (number)
- `is_published` (boolean)

## 4.3 Homepage featured blocks
**Collection: `homepage_features`**
- `id` (string/uuid)
- `key` (enum: `bible`, `timeline`, `tools`)
- `title` (string)
- `description` (string)
- `cta_label` (string)
- `cta_url` (string)
- `order` (number; fixed 1..3)
- `is_published` (boolean)

## 4.4 Resource items
**Collection: `resources`**
- `id` (string/uuid)
- `title` (string)
- `description` (rich text or plain text)
- `image_url` (string)
- `cta_label` (string)
- `cta_url` (string)
- `category` (string, optional)
- `tags` (array of strings, optional)
- `order` (number)
- `is_published` (boolean)

## 4.5 Testimonies
**Collection: `testimonies`**
- `id` (string/uuid)
- `person_name` (string)
- `title` (string, optional)
- `excerpt` (text)
- `youtube_url` (string)
- `thumbnail_url` (string, optional)
- `order` (number)
- `is_published` (boolean)

## 4.6 Timeline entries
**Collection: `timeline_entries`**
- `id` (string/uuid)
- `period_label` (string) — e.g., “Patriarches”
- `start_sort_key` (number/string sortable)
- `end_sort_key` (number/string, optional)
- `display_date` (string) — human-readable label
- `title` (string)
- `description` (rich text/plain)
- `media_url` (string, optional)
- `reference_links` (array of links, optional)
- `group_key` (string, optional)
- `order_override` (number, optional)
- `is_published` (boolean)

---

## 5) Simple CMS / Admin Model

## 5.1 Admin content types
Minimum editable content types (matching requested scope):
1. Hero slides
2. Resource items
3. Testimonies
4. Timeline entries

Recommended supporting type:
5. Site settings (navigation + footer blocks)

## 5.2 Admin roles
1. **Admin**
   - Full CRUD on all collections
   - Publish/unpublish
   - Reorder items
2. **Editor**
   - CRUD on content collections
   - Cannot modify roles/system settings
3. **Viewer/Reviewer** (optional)
   - Read-only in admin

## 5.3 Admin workflows
1. **Draft → Review → Published** lifecycle per item
2. **Ordering controls** for lists (slides/resources/testimonies/timeline)
3. **Link validation** for CTA and YouTube URLs
4. **Preview mode** for each template using draft content

## 5.4 Minimal governance rules
- Required fields validation per collection
- One-click publish toggle
- Soft delete / archive rather than hard delete (recommended)
- Basic change history (updated_by, updated_at)

---

## 6) Navigation Map (Information Architecture)

## 6.1 Primary routes
- `/` → Homepage
- `/ressources` (or existing resource route) → Resource page
- `/frise-biblique` (or existing timeline route) → Timeline page

## 6.2 Homepage pathway links
- Featured block “Se procurer une Bible” → target resource/guide page
- Featured block “Frise biblique” → timeline page
- Featured block “Outils” → resource/tool listing page

## 6.3 Footer map
- **À propos** → about content page/section
- **Références** → references page/section
- **Contact** → contact page/form/links

---

## 7) Template-to-Data Binding Matrix

1. **Homepage template**
   - `site_settings.primary_nav`
   - `hero_slides` (published, ordered)
   - `homepage_features` (published, ordered)
   - `testimonies` (published, ordered)
   - `site_settings.footer_*`

2. **Resource template**
   - `site_settings.primary_nav`
   - `resources` (published, ordered)
   - `site_settings.footer_*`

3. **Timeline template**
   - `site_settings.primary_nav`
   - `timeline_entries` (published, sorted by key/order)
   - `site_settings.footer_*`

---

## 8) Implementation Notes (Non-visual)

- Keep current UI hierarchy; only abstract repeated blocks into reusable components.
- Keep existing class names/CSS hooks where possible to avoid style drift.
- Keep route URLs unchanged unless already inconsistent.
- Build content adapters so page templates consume normalized data regardless of backend source.
