# Changelog

All notable changes to the project will be documented in this file.


The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)

### Added
- Implemented batch downloading functionality for project data, supporting custom archive zip naming.
- Added support for aggregating different data types into a single CSV per day.
- Added the ability to download cached files via an endpoint, providing download URLs.
- Implemented functionality to delete cached files for a specific device in a project.
- Added an endpoint to retrieve cached files for a project with device filtering.
- Enhanced download data handler to support custom CSV file naming based on the archive name parameter.

### Changed
- Updated the `downloadDataHandler` to use query parameters for GET requests
- Improved error handling and logging across various functions to ensure consistent and informative logs
- Refactored the `downloadDataHandler` to aggregate data types into daily CSV's, including timestamp and data type columns.

### Fixed
- Resolved issues with date range processing in the `getDateRange` function.
- Addressed problems with file paths and folder creation during pbatch downloading and zipping process.
- Fixed errors related to missing or invalid query parameters in download and caching functions.
- Corrected logic for determining and applying the detail level in data fetching functions.
