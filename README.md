# Palmer Jones Website Development Guide

This guide contains everything needed to work with your personal website locally and deploy changes to production.

## Local Development Setup

### Prerequisites
- Ruby (Jekyll requires Ruby to run)
- Bundler (Ruby package manager)
- Git (for version control)

### Initial Setup Steps
1. Clone the repository:
   ```bash
   git clone https://github.com/palmerjones0/palmerjones0.github.io.git .
   ```

2. Install required dependencies:
   ```bash
   bundle install
   ```

### Daily Development Workflow

1. **Start the local server**
   First, check if a server is already running:
   ```bash
   lsof -i :4000
   ```
   If you see output, a server is already running on port 4000. You can either:
   - Use the existing server (recommended)
   - Stop the existing server with Ctrl+C in its terminal window

   To start the server (if none is running):
   ```bash
   bundle exec jekyll serve --livereload --port 4000
   ```
   This will start a local server at http://localhost:4000
   The --livereload flag makes the site automatically refresh when you make changes
   The --port flag ensures the server always uses port 4000

2. **Making Changes**
   - Edit files in your favorite text editor (like VS Code)
   - Changes will appear automatically in your browser
   - Common files you might edit:
     - `_posts/` - Contains all your blog posts
     - `_config.yml` - Main configuration file
     - `_pages/` - Contains your static pages
     - `assets/` - Contains images and other media

3. **Creating New Posts**
   - Posts go in the `_posts/` directory
   - File names must follow the format: `YYYY-MM-DD-title.md`
   - Each post needs front matter at the top:
     ```yaml
     ---
     title: "Your Post Title"
     date: YYYY-MM-DD
     categories:
       - category1
       - category2
     tags:
       - tag1
       - tag2
     ---
     ```

### Deploying to Production

1. **Save your changes**
   ```bash
   git add .
   git commit -m "Describe your changes here"
   git push origin main
   ```

2. **Wait for deployment**
   - GitHub Pages will automatically build and deploy your site
   - This usually takes 1-2 minutes
   - You can check the status in your repository's "Actions" tab
   - Once complete, changes will be live at palmercjones.com

### Common Tasks

#### Adding Images
1. Place images in `assets/images/`
2. Reference in posts/pages using:
   ```markdown
   ![Image description](/assets/images/your-image.jpg)
   ```

#### Modifying Site Configuration
1. Open `_config.yml`
2. Make changes (common settings include):
   - Site title and description
   - Author information
   - Social media links
   - Navigation menu items
3. Save and wait for the local server to rebuild

#### Creating New Pages
1. Add new .md files to `_pages/` directory
2. Include front matter:
   ```yaml
   ---
   title: "Page Title"
   permalink: /url-path/
   ---
   ```

### Troubleshooting

If the local server isn't working:
1. Check if another server is already using port 4000:
   ```bash
   lsof -i :4000
   ```
2. If port 4000 is in use:
   - Find the terminal running the existing server and use it, or
   - Stop the existing server with Ctrl+C before starting a new one
3. If no server is running:
   - Stop any failed server attempts with Ctrl+C
   - Run `bundle install` to ensure all dependencies are up to date
   - Try starting the server again with `bundle exec jekyll serve --livereload --port 4000`

If changes aren't appearing:
1. Check if the local server is running
2. Make sure you saved the file
3. Try clearing your browser cache
4. Check the terminal for error messages

### Development Best Practices

1. **Browser Management**
   - Open http://localhost:4000 once at the start of your development session
   - Use browser refresh (F5) to see changes instead of reopening the URL
   - If changes aren't appearing after refresh, try hard refresh (Ctrl+F5 or Cmd+Shift+R) to clear the browser cache

2. **Testing Changes**
   - Always test your own changes thoroughly
   - Check your changes across different pages and features
   - Test on different screen sizes using browser dev tools
   - Verify all links and navigation work as expected
   - AI tools will help with development but leave final testing to you

### Need Help?

If you need assistance:
1. Check the [Minimal Mistakes theme documentation](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
2. Look for error messages in the terminal
3. Use the GitHub repository's Issues tab for theme-specific questions
