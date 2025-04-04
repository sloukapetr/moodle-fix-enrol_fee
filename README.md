# FIX MOODLE PLUGIN - ENROL FEE
When then unenrolment in enrol after payment original moodle plugin is not working, this is a small guide, how to fix that.
Worked MOODLE 405

## Step by step all in /moodle/enrol/fee
- Make `/moodle/enrol/fee/classes/task/sync_enrolments.php` and in that file.

```php
<?php
// This file is part of Moodle - http://moodle.org/
//
// Moodle is free software: you can redistribute it and/or modify
// it under the terms of the GNU General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// (at your option) any later version.
//
// Moodle is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License
// along with Moodle.  If not, see <http://www.gnu.org/licenses/>.

/**
 * Sync enrolments task
 * @package   enrol_fee
 * @copyright 2025 Petr Slouka <sloukapetr@gmail.com>
 * @license   http://www.gnu.org/copyleft/gpl.html GNU GPL v3 or later
 */

namespace enrol_fee\task;

defined('MOODLE_INTERNAL') || die();

/**
 * Class sync_enrolments
 * @package   enrol_fee
 * @copyright 2025 Petr Slouka <sloukapetr@gmail.com>
 * @license   http://www.gnu.org/copyleft/gpl.html GNU GPL v3 or later
 */
class sync_enrolments extends \core\task\scheduled_task {

    /**
     * Name for this task.
     *
     * @return string
     */
    public function get_name() {
        return get_string('syncenrolmentstask', 'enrol_fee');
    }

    /**
     * Run task for synchronising users.
     */
    public function execute() {
        $trace = new \text_progress_trace();
        if (!enrol_is_enabled('fee')) {
            $trace->output('Plugin not enabled');
            return;
        }
        $enrol = enrol_get_plugin('fee');
        $enrol->sync($trace);
    }
}
```

- Make `/moodle/enrol/fee/db/tasks.php` and in that file.

```php
<?php
// This file is part of Moodle - http://moodle.org/
//
// Moodle is free software: you can redistribute it and/or modify
// it under the terms of the GNU General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// (at your option) any later version.
//
// Moodle is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License
// along with Moodle.  If not, see <http://www.gnu.org/licenses/>.

/**
 * Task definition for enrol_database.
 * @package   enrol_fee
 * @copyright 2025 Petr Slouka <sloukapetr@gmail.com>
 * @license   http://www.gnu.org/copyleft/gpl.html GNU GPL v3 or later
 */

defined('MOODLE_INTERNAL') || die();

$tasks = [
    [
        'classname' => '\enrol_fee\task\sync_enrolments',
        'blocking' => 0,
        'minute' => '*',
        'hour' => '*',
        'day' => '*',
        'month' => '*',
        'dayofweek' => '*',
    ],
];
```

- (Optional) `/moodle/enrol/fee/lang/en/enrol_fee.php` add following line if you want to see the translation in your moodle application.
```php
$string['syncenrolmentstask'] = 'Synchronise fee enrolments task';
```

- In the file `/moodle/enrol/fee/version.php` incriement parameter `$plugin->version` at least by one.

- Now go to the administration of moodle and update your moodle database.
