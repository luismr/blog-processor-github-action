
# Blog Processing GitHub Action

```gherkin
Feature: Blog Processing GitHub Action
  As a user,
  I want to create, update, and manage blog posts in Markdown format,
  So that they are automatically published as HTML on GitHub Pages.

  # -----------------------------
  # Use Case 1: Creating a New Blog Post
  # -----------------------------

  Scenario: User adds a new blog post
    Given a user creates a new folder "/articles/my-first-blog-post/"
    And the folder contains "article.md" with Markdown content
    And the folder contains "article.yaml" with metadata:
      | title                   | description                   | author       | created_at           | updated_at           |
      | My First Post           | Introduction to my new blog.  | GitHub User  | 2025-03-05T12:00:00Z | 2025-03-05T12:00:00Z |
      | How use Github Pages    | Introduction to github pages. | GitHub User  | 2025-03-05T12:00:00Z | 2025-03-05T12:00:00Z |
    When the user commits and pushes the folder to the repository
    Then the GitHub Action converts "article.md" to HTML
    And saves it to "/blog/2025/03/my-first-blog-post.html"
    And updates the blog index

  Scenario: Edge Case - Missing "article.yaml"
    Given a new folder "/articles/missing-metadata-post/" contains only "article.md"
    When the GitHub Action runs
    Then it logs an error "Missing article.yaml in /articles/missing-metadata-post/"
    And does not process the article

  Scenario: Edge Case - Incorrect YAML Format
    Given "article.yaml" in "/articles/invalid-metadata/" contains invalid YAML syntax
    When the GitHub Action runs
    Then it logs an error "Invalid YAML format in /articles/invalid-metadata/article.yaml"
    And does not process the article

  Scenario: Edge Case - Article Folder Without a Markdown File
    Given "/articles/missing-content/" contains only "article.yaml"
    When the GitHub Action runs
    Then it logs an error "Missing article.md in /articles/missing-content/"
    And does not process the article

  # -----------------------------
  # Use Case 2: Updating an Existing Blog Post
  # -----------------------------

  Scenario: User modifies an existing article
    Given a blog post exists in "/articles/my-first-blog-post/"
    And "article.md" is updated with new content
    When the user commits and pushes the changes
    Then the GitHub Action updates "/blog/2025/03/my-first-blog-post.html"
    And updates "updated_at" in "article.yaml"
    And preserves the original "created_at" timestamp

  Scenario: Edge Case - Empty article.md File
    Given "article.md" in "/articles/empty-post/" is empty
    When the GitHub Action runs
    Then it logs a warning "article.md is empty in /articles/empty-post/"
    And does not generate an HTML file

  Scenario: Edge Case - Duplicate Article Titles
    Given two folders "/articles/duplicate-post1/" and "/articles/duplicate-post2/"
    And both contain "article.yaml" with the same title "My Duplicate Post"
    When the GitHub Action runs
    Then it logs an error "Duplicate title detected: My Duplicate Post"
    And does not generate HTML for the duplicate post

  # -----------------------------
  # Use Case 3: Handling Images in Blog Posts
  # -----------------------------

  Scenario: User adds an image to an article
    Given "article.md" contains an image reference "![My Image](./image.png)"
    And "image.png" is placed in "/articles/my-first-blog-post/"
    When the GitHub Action processes the article
    Then "image.png" is moved to "/blog/2025/03/my-first-blog-post_img00001.png"
    And the image reference in HTML is updated accordingly

  Scenario: Edge Case - Image Referenced but Missing
    Given "article.md" in "/articles/missing-image-post/" contains:
      """md
      ![My Image](./missing-image.png)
      """
    When the GitHub Action runs
    Then it logs an error "Referenced image missing-image.png not found"
    And does not process the article

  Scenario: Edge Case - Large Image Files
    Given "article.md" in "/articles/large-image-post/" references "large-image.png"
    And "large-image.png" exceeds 5MB
    When the GitHub Action runs
    Then it logs a warning "Skipping large-image.png: File size exceeds 5MB"
    And does not upload the image

  # -----------------------------
  # Use Case 4: Deploying the Blog to GitHub Pages
  # -----------------------------

  Scenario: Blog is published after processing
    Given blog posts exist in "/blog/"
    When the GitHub Action runs successfully
    Then it commits the processed HTML files to the "gh-pages" branch
    And the blog is accessible at "https://username.github.io/blog/"

  Scenario: Edge Case - GitHub Username Not Available
    Given a commit updates an article but has no associated GitHub username
    When the GitHub Action runs
    Then it logs a warning "Author not found, setting as Unknown Author"
    And assigns "Unknown Author" in the metadata

  # -----------------------------
  # Use Case 5: Generating an Index Page
  # -----------------------------

  Scenario: Index page is updated with new articles
    Given new articles are added to "/blog/"
    When the GitHub Action runs
    Then it updates "/blog/index.html" with a list of all posts
    And displays the posts sorted by date

  Scenario: Edge Case - Unsupported File Formats
    Given "/articles/unsupported-file-post/" contains "document.pdf"
    When the GitHub Action runs
    Then it logs a warning "Ignoring unsupported file: document.pdf"
    And does not process the file

```